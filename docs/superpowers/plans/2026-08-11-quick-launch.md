# Quick Launch Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add an app-only Command-K palette that finds library games, launches ready games, reveals setup or detail pages, and opens Add Game or Settings.

**Architecture:** A pure `QuickLaunchIndex` converts the live library state into ordered, searchable entries and decides each game's safe default action. `QuickLaunchView` renders those entries as a focused in-window overlay, while `ContentView` owns presentation and routes every action into the existing navigation, sheet, and `BottleManager.launch` paths. `CorvusArcadeApp` contributes only the native Command-K menu command and a notification request, matching the existing Settings command architecture.

**Tech Stack:** Swift 6, SwiftUI, AppKit, XCTest, Apple String Catalogs, macOS 14+

## Global Constraints

- Support macOS 14 and later.
- Keep Quick Launch app-only. Do not register a global shortcut, create another window, add a login item, or run a helper process.
- Match locally against the in-memory library. Do not add network work, timers, debouncing, or a second library cache.
- Reuse the existing game launch, game detail, Add Game sheet, and in-window Settings paths.
- Ignore Command-K while a sheet, alert, confirmation dialog, or other modal presentation is active.
- Use the active Corvus Arcade theme and its existing Increase Contrast behavior.
- Respect Reduce Motion and provide complete keyboard and VoiceOver behavior.
- Keep every shipped locale in parity for new interface strings. Translation batch submission requires explicit permission in the implementation turn.
- Preserve all existing user changes. Stage only files named by the active task and never push the public remote.

---

### Task 1: Search, Ordering, and Safe Default Actions

**Files:**
- Create: `Sources/CorvusArcade/QuickLaunchIndex.swift`
- Create: `Tests/CorvusArcadeTests/QuickLaunchIndexTests.swift`

**Interfaces:**
- Produces: `QuickLaunchGameState: Equatable` with `running`, `ready(GraphicsBackend)`, `incomplete`, and `notInstalled`
- Produces: `QuickLaunchEntry.Kind: Hashable` with `game(Game.ID)`, `addGame`, and `settings`
- Produces: `QuickLaunchEntry` with `id`, `kind`, `title`, and optional `gameState`
- Produces: `QuickLaunchDefaultAction: Equatable` with `launch` and `reveal`
- Produces: `QuickLaunchIndex.entries(games:favorites:installed:running:query:addGameTitle:settingsTitle:) -> [QuickLaunchEntry]`
- Produces: `QuickLaunchIndex.defaultAction(for:) -> QuickLaunchDefaultAction?`

- [ ] **Step 1: Write failing ordering and matching tests**

Create deterministic games with fixed UUIDs, including `Café Racer`, one favorite, one ready game, and one not-installed game. Add these exact assertions:

```swift
func testEmptyQueryPutsFavoritesFirstAndUtilitiesLast() {
    let result = QuickLaunchIndex.entries(
        games: [ready, favorite, missing],
        favorites: [favorite.id], installed: [ready.id, favorite.id], running: [],
        query: "", addGameTitle: "Add Game", settingsTitle: "Settings")
    XCTAssertEqual(result.map(\.kind), [
        .game(favorite.id), .game(ready.id), .game(missing.id), .addGame, .settings,
    ])
}

func testSearchIgnoresCaseAndDiacritics() {
    let result = QuickLaunchIndex.entries(
        games: [cafe], favorites: [], installed: [cafe.id], running: [],
        query: "CAFE", addGameTitle: "Add Game", settingsTitle: "Settings")
    XCTAssertEqual(result.map(\.kind), [.game(cafe.id)])
}

func testUtilityTitlesParticipateInSearch() {
    let result = QuickLaunchIndex.entries(
        games: [], favorites: [], installed: [], running: [],
        query: "settings", addGameTitle: "Add Game", settingsTitle: "Settings")
    XCTAssertEqual(result.map(\.kind), [.settings])
}

func testNoMatchStillOffersAddGame() {
    let result = QuickLaunchIndex.entries(
        games: [ready], favorites: [], installed: [ready.id], running: [],
        query: "unknown title", addGameTitle: "Add Game", settingsTitle: "Settings")
    XCTAssertEqual(result.map(\.kind), [.addGame])
}
```

- [ ] **Step 2: Run the focused tests to verify failure**

Run: `swift test --filter QuickLaunchIndexTests`

Expected: compilation fails because `QuickLaunchIndex` and its entry types do not exist.

- [ ] **Step 3: Implement the pure entry index**

Create the public-to-module types named above. In `entries`, trim whitespace, fold both query and candidate titles with:

```swift
text.folding(options: [.caseInsensitive, .diacriticInsensitive], locale: .current)
```

Preserve the input order inside favorite and non-favorite groups. Derive game state in this priority order: running, not installed, incomplete because `game.needsExe`, then ready with `game.backend`. Filter game and utility titles locally. When a non-empty query produces no game or utility match, return one `.addGame` entry as the recovery action.

- [ ] **Step 4: Add failing state and default-action tests**

```swift
func testStatesReflectLiveLibrarySetsAndExecutableReadiness() {
    XCTAssertEqual(entry(for: running, installed: true, running: true).gameState, .running)
    XCTAssertEqual(entry(for: ready, installed: true).gameState, .ready(ready.backend))
    XCTAssertEqual(entry(for: incomplete, installed: true).gameState, .incomplete)
    XCTAssertEqual(entry(for: missing, installed: false).gameState, .notInstalled)
}

func testOnlyReadyGameDefaultsToLaunch() {
    XCTAssertEqual(QuickLaunchIndex.defaultAction(for: readyEntry), .launch)
    XCTAssertEqual(QuickLaunchIndex.defaultAction(for: runningEntry), .reveal)
    XCTAssertEqual(QuickLaunchIndex.defaultAction(for: incompleteEntry), .reveal)
    XCTAssertEqual(QuickLaunchIndex.defaultAction(for: missingEntry), .reveal)
    XCTAssertNil(QuickLaunchIndex.defaultAction(for: addGameEntry))
}
```

- [ ] **Step 5: Implement default-action selection and run tests**

Implement `defaultAction(for:)` as an exhaustive switch. Return `.launch` only for `.game` entries whose state is `.ready`; return `.reveal` for every other game state; return `nil` for utility entries.

Run: `swift test --filter QuickLaunchIndexTests`

Expected: every Quick Launch index test passes with zero failures.

- [ ] **Step 6: Commit the pure model checkpoint**

```bash
git add Sources/CorvusArcade/QuickLaunchIndex.swift Tests/CorvusArcadeTests/QuickLaunchIndexTests.swift
git diff --cached --check
git commit -m "feat: add quick launch search model"
```

### Task 2: Centered Palette Interface

**Files:**
- Create: `Sources/CorvusArcade/Views/QuickLaunchView.swift`
- Modify: `Sources/CorvusArcade/Views/ContentView.swift:215-315`

**Interfaces:**
- Consumes: all Task 1 entry and action types
- Consumes: `BottleManager.games`, `favorites`, `installedIDs`, `running`, `art`, and `launch(_:)`
- Produces: `QuickLaunchView(onReveal:onLaunch:onAddGame:onSettings:onDismiss:)`
- Produces: `ContentView` state `showQuickLaunch` and `showQuickLaunchAddGame`

- [ ] **Step 1: Add the palette view shell and focus contract**

Create `QuickLaunchView` with these stored callbacks:

```swift
let onReveal: (Game.ID) -> Void
let onLaunch: (Game.ID) -> Void
let onAddGame: () -> Void
let onSettings: () -> Void
let onDismiss: () -> Void
```

Read `BottleManager` through `@EnvironmentObject`. Add `@State private var query = ""`, `@State private var selected: QuickLaunchEntry.Kind?`, and `@FocusState private var searchFocused: Bool`. Build entries from the live published sets, using localized Add Game and Settings titles. On appearance, select the first result and focus the search field. When entries change, keep the current selection if it still exists, otherwise select the nearest surviving index, falling back to the first result.

- [ ] **Step 2: Build the themed presentation**

Implement this hierarchy:

```swift
ZStack {
    Theme.void_.opacity(Theme.isLight ? 0.34 : 0.68)
        .ignoresSafeArea()
        .onTapGesture(perform: onDismiss)

    VStack(spacing: 0) {
        searchField
        Divider().overlay(Theme.line)
        resultsList
        Divider().overlay(Theme.line)
        keyboardFooter
    }
    .frame(width: 620)
    .frame(maxHeight: 520)
    .background(Theme.panel.opacity(0.98), in: RoundedRectangle(cornerRadius: 16))
    .overlay(RoundedRectangle(cornerRadius: 16).strokeBorder(Theme.line))
    .shadow(color: .black.opacity(0.45), radius: 36, y: 18)
}
```

Use the existing `ArtImage` at 40 by 40 points for games. Use SF Symbols `plus.square` and `gearshape` for utility rows. Show one localized state line per game and show the backend label for `.ready`. Mark the selected row with both a themed surface change and `.accessibilityAddTraits(.isSelected)`. Keep every row at least 44 points high.

- [ ] **Step 3: Add keyboard behavior**

Attach macOS 14 `onKeyPress` handlers to the focused search area:

- Up Arrow selects the previous entry, clamped at the first.
- Down Arrow selects the next entry, clamped at the last.
- Return performs the selected entry's default action.
- Command-Return reveals a selected game regardless of its state.
- Escape calls `onDismiss`.
- Command-K while open restores `searchFocused = true` and returns `.handled`.

Move the selected row into view with `ScrollViewReader`. The footer must use localized action names plus symbolic key caps, not long instructional prose.

- [ ] **Step 4: Add accessibility and motion behavior**

Read `@Environment(\.accessibilityReduceMotion)`. Use opacity plus a subtle scale transition only when Reduce Motion is false, and opacity only otherwise. Give the search field a localized label. Combine each result row into one accessibility element whose label includes game name, state, and current default action. Announce the empty state with Add Game as its available action. Do not use artwork or status color as the sole state indicator.

- [ ] **Step 5: Integrate the overlay at the root**

In `ContentView`, add:

```swift
@State private var showQuickLaunch = false
@State private var showQuickLaunchAddGame = false
```

Wrap the existing `NavigationSplitView` in a root `ZStack`. Overlay `QuickLaunchView` when `showQuickLaunch` is true. Route callbacks as follows:

- reveal: set `selection = .game(id)` and dismiss
- launch: resolve the current game by ID, dismiss, then call `Task { await bottles.launch(game) }`
- Add Game: dismiss and set `showQuickLaunchAddGame = true`
- Settings: set `selection = .settings` and dismiss
- dismiss: set `showQuickLaunch = false`

Host one new root sheet:

```swift
.sheet(isPresented: $showQuickLaunchAddGame) {
    AddGameView().environmentObject(bottles)
}
```

Do not remove the existing sidebar or Home Add Game entry points.

- [ ] **Step 6: Build and manually inspect the palette**

Run: `swift build`

Expected: successful build for macOS 14.

Launch the development app. Verify the palette remains centered while resizing from the 900 by 700 minimum through the 1320 by 900 default. Check all eight themes, long game names, no artwork, empty library filtering, keyboard-only traversal, Full Keyboard Access, VoiceOver labels, Increase Contrast, and Reduce Motion.

- [ ] **Step 7: Commit the palette checkpoint**

```bash
git add Sources/CorvusArcade/Views/QuickLaunchView.swift Sources/CorvusArcade/Views/ContentView.swift
git diff --cached --check
git commit -m "feat: add centered quick launch palette"
```

### Task 3: Command-K Routing and Modal Safety

**Files:**
- Modify: `Sources/CorvusArcade/CorvusArcadeApp.swift:81-118`
- Modify: `Sources/CorvusArcade/Views/ContentView.swift:215-315`
- Test: `Tests/CorvusArcadeTests/QuickLaunchIndexTests.swift`

**Interfaces:**
- Produces: `Notification.Name.openQuickLaunch`
- Consumes: `ContentView.showQuickLaunch`

- [ ] **Step 1: Add the app command**

In `CorvusArcadeApp.commands`, add a command group after the existing app Settings command:

```swift
CommandMenu(L("Game")) {
    Button(L("Quick Launch")) {
        NotificationCenter.default.post(name: .openQuickLaunch, object: nil)
    }
    .keyboardShortcut("k", modifiers: .command)
}
```

Add `Notification.Name.openQuickLaunch` beside `openSettings`, with the stable raw name `CorvusArcade.openQuickLaunch`.

- [ ] **Step 2: Gate presentation against modal UI**

In `ContentView`, receive `.openQuickLaunch`. Before presenting, require all of these to be false or nil:

```swift
showQuickLaunchAddGame
bottles.pendingRemoval != nil
bottles.pendingRename != nil
bottles.crashedGame != nil
NSApp.modalWindow != nil
NSApp.keyWindow?.attachedSheet != nil
```

If any gate is active, return without changing focus or navigation. Otherwise set `showQuickLaunch = true`. This preserves the existing modal as the only active decision.

- [ ] **Step 3: Verify live-state action routing**

Before executing a selected game, resolve it again from `bottles.games` by ID and derive its current state from `running`, `installedIDs`, and `needsExe`. Launch only if it is still ready. If it became running, missing, or incomplete, reveal its detail page instead. This prevents a stale palette row from bypassing the safe default-action rule.

- [ ] **Step 4: Run automated and app-level checks**

Run:

```bash
swift test --filter QuickLaunchIndexTests
swift build
```

Expected: all focused tests pass and the build exits successfully.

In the app, verify Command-K from Home, game detail, and Settings. Open Add Game, a removal confirmation, and a rename alert in turn and verify Command-K does nothing until each modal closes. Verify Command-Comma and Command-N still reach their existing destinations.

- [ ] **Step 5: Commit command routing**

```bash
git add Sources/CorvusArcade/CorvusArcadeApp.swift Sources/CorvusArcade/Views/ContentView.swift Tests/CorvusArcadeTests/QuickLaunchIndexTests.swift
git diff --cached --check
git commit -m "feat: connect quick launch command"
```

### Task 4: Localization Parity

**Files:**
- Modify: `Sources/CorvusArcade/Localization/Localizable.xcstrings`
- Create temporarily outside the repository: `/private/tmp/corvus-quick-launch-strings.json`

**Interfaces:**
- Consumes: every new `L(...)` key introduced by Tasks 2 and 3
- Reuses existing localized keys: `Add Game`, `Settings`, `Running`, `Ready`, `Not installed`, `Launch`, and `Search`

- [ ] **Step 1: Inventory only the new source-string delta**

Use source and catalog searches to produce an exact list of missing keys. Keep new copy minimal. The expected new concepts are `Game` as the command-menu title, `Quick Launch`, the incomplete setup state, and the reveal action. Reuse existing catalog keys wherever their meaning matches.

- [ ] **Step 2: Add English catalog entries with context**

Add manual English entries for only the missing keys. Give each entry a comment identifying whether it is a menu title, command, game state, shortcut action, search-field accessibility label, or empty state. Do not overwrite any existing localization.

- [ ] **Step 3: Request current-turn permission for translation submission**

Present the exact new key count and locale list to the user. Do not submit a Corvus Translate batch until the user explicitly authorizes translation submission in that implementation turn.

- [ ] **Step 4: Translate and merge the authorized delta**

After authorization, use the `translate` and `localize-xcstrings` skills. Submit only the flat missing-key delta in UI mode for every non-English locale already present in the catalog. Merge with `scripts/merge-translations.py` so existing entries remain unchanged.

- [ ] **Step 5: Audit the destination catalog**

Run the catalog structural verifier, placeholder and markup checks, brand guard, destination correctness audit, locale parity count, and long-dash audit. Read every new translated value in the destination catalog, checking especially that Launch means start a game, Show means navigate to a game page, and Game is a menu noun. Correct wrong-sense output through the translation workflow.

- [ ] **Step 6: Commit localization separately**

```bash
git add Sources/CorvusArcade/Localization/Localizable.xcstrings
git diff --cached --check
git commit -m "l10n: translate quick launch interface"
```

### Task 5: Full Verification and Private Checkpoint

**Files:**
- Verify all files changed by Tasks 1 through 4
- Modify only if needed for release messaging: `Sources/CorvusArcade/Views/WhatsNew.swift`

**Interfaces:**
- Consumes: the complete Quick Launch feature

- [ ] **Step 1: Run the complete automated verification**

Run fresh:

```bash
swift test
swift build
git diff --check
~/.corvus-tools/git-tools/audit-emdash.sh /Users/ale/Documents/CorvusArcade
```

Expected: all tests pass, the build exits successfully, the feature diff has no whitespace errors, and no new long-dash violation is introduced. Report the pre-existing mockup violation separately if it remains unrelated.

- [ ] **Step 2: Verify every behavior from the approved specification**

Launch a development build and check:

- Command-K opens from Home, detail, and Settings.
- Repeating Command-K restores search focus.
- Escape and scrim clicks dismiss without changing the selected page.
- Favorite-first stable ordering and case and diacritic-insensitive matching work.
- A ready game launches through the existing warning and progress path.
- A running game, missing installation, or incomplete executable opens its detail page without launching.
- Command-Return always reveals the game detail page.
- Add Game and Settings use their existing destinations.
- Active modal UI blocks Command-K.
- Removing or changing a game while the palette is open recovers selection safely.
- All themes, supported locales, keyboard navigation, Full Keyboard Access, VoiceOver, Increase Contrast, and Reduce Motion behave as specified.

- [ ] **Step 3: Run a resource audit**

Launch the production-style app build, obtain its PID, and run `vmmap PID --summary` for physical footprint. Confirm Quick Launch adds no idle timer, task, network request, background helper, or meaningful persistent memory cost. Record a one-line resource verdict.

- [ ] **Step 4: Decide release-note scope**

If Quick Launch is shipping in the next release, add one concise localized What's New item describing Command-K and direct library access, then run the existing changelog synchronization and localization parity workflow. If no release is being prepared, leave `WhatsNew.swift` unchanged and record that release messaging is deferred rather than adding speculative copy.

- [ ] **Step 5: Inspect and checkpoint privately**

Run `git status --short`. Verify every staged path belongs to Quick Launch and inspect the full staged diff. Run the credential scan and git security checks. Commit only remaining named Quick Launch files to the private `origin` history. Do not push either remote and do not stage the pre-existing installer diagnostics, icon sources, `.claude`, backup script, or unrelated plans.
