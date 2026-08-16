# Settings Navigation Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Replace the long Settings page with four native category tabs and add an immediately reversible menu bar visibility preference.

**Architecture:** `SettingsView` owns the selected category and renders one category-specific scroll view while retaining the existing setting sections and persistence. A small shared preferences type defines the menu bar key and default, and `CorvusArcadeApp` binds that value to SwiftUI's supported `MenuBarExtra.isInserted` API.

**Tech Stack:** Swift 6, SwiftUI, AppStorage, Swift Testing, Apple String Catalogs

## Global Constraints

- Support macOS 14 and later.
- Use standard SwiftUI controls for keyboard and VoiceOver behavior.
- Preserve all existing preference defaults, alerts, backup safeguards, and setting behavior.
- Menu bar visibility defaults to enabled and changes immediately without relaunching.
- Keep all shipped locales in parity for new interface strings.
- Do not modify or stage unrelated untracked files.

---

### Task 1: Shared Settings Model

**Files:**
- Create: `Sources/CorvusArcade/SettingsPreferences.swift`
- Create: `Tests/CorvusArcadeTests/SettingsPreferencesTests.swift`

**Interfaces:**
- Produces: `SettingsCategory: String, CaseIterable, Identifiable`
- Produces: `SettingsCategory.title: String.LocalizationValue`
- Produces: `SettingsPreferences.showMenuBarExtraKey: String`
- Produces: `SettingsPreferences.showMenuBarExtraDefault: Bool`

- [ ] **Step 1: Add focused model tests**

Verify the ordered category identifiers are `general`, `graphics`, `captures`, and `storageAdvanced`; verify their visible title keys; verify the menu bar key is `showMenuBarExtra`; and verify its default is `true`.

- [ ] **Step 2: Implement the shared types**

Define the four cases and switch-based title values. Keep stable identifiers separate from localized display values. Define the menu bar preference key and default in one place.

- [ ] **Step 3: Run focused tests**

Run: `swift test --filter SettingsPreferencesTests`

Expected: all Settings preference and category tests pass with zero failures.

### Task 2: Category-Based Settings Interface

**Files:**
- Modify: `Sources/CorvusArcade/Views/SettingsView.swift:3-102`
- Test: `Tests/CorvusArcadeTests/SettingsPreferencesTests.swift`

**Interfaces:**
- Consumes: `SettingsCategory`
- Consumes: `SettingsPreferences.showMenuBarExtraKey`
- Consumes: `SettingsPreferences.showMenuBarExtraDefault`

- [ ] **Step 1: Add category and menu bar state**

Add `@State private var category: SettingsCategory = .general` and an `@AppStorage` property for menu bar visibility using the shared key and default.

- [ ] **Step 2: Replace the single long scroll view**

Keep the Settings title and description above a segmented `Picker`. Render one scroll view below it with these exact groups:

- General: behavior, appearance, optional updates, and menu bar visibility
- Graphics: graphics and the existing per-game override note
- Screenshots: screenshot settings
- Storage & Advanced: bottles, artwork, backup, and advanced diagnostics

Move existing section view builders without changing their control logic. When the theme requests `returnToAppearance`, select General before scrolling to Appearance.

- [ ] **Step 3: Add the menu bar row**

Add a General section containing a standard toggle titled `Show Corvus Arcade in the menu bar`. Explain that it provides quick library and running-game access. The toggle must remain available in the main Settings view after the item is hidden.

- [ ] **Step 4: Validate source structure**

Use a source audit to confirm every existing section appears in exactly one category and no category uses custom click handling.

### Task 3: Live Menu Bar Visibility

**Files:**
- Modify: `Sources/CorvusArcade/CorvusArcadeApp.swift:13-16,104-106`

**Interfaces:**
- Consumes: `SettingsPreferences.showMenuBarExtraKey`
- Consumes: `SettingsPreferences.showMenuBarExtraDefault`

- [ ] **Step 1: Bind the app-scoped preference**

Add an app-scoped `@AppStorage` property using the shared key and default.

- [ ] **Step 2: Bind `MenuBarExtra` insertion**

Pass the projected preference binding to `MenuBarExtra` through its `isInserted` argument. Do not conditionally construct a different scene and do not stop any bottle or telemetry work when it changes.

- [ ] **Step 3: Build the app**

Run: `swift build`

Expected: successful build for the package's macOS 14 deployment target.

### Task 4: Localization Parity

**Files:**
- Modify: `Sources/CorvusArcade/Localization/Localizable.xcstrings`

**Interfaces:**
- Consumes: the source keys emitted by `SettingsCategory.title` and the menu bar toggle row

- [ ] **Step 1: Inventory the catalog**

Record the existing locale set and identify only the new Settings keys that lack translations.

- [ ] **Step 2: Add source entries with context**

Add manual catalog entries for `General`, `Storage & Advanced`, `Show Corvus Arcade in the menu bar`, and the menu bar help sentence. Reuse the existing fully localized `Screenshots` key. Add comments that distinguish tab labels from prose and explain the macOS menu bar meaning.

- [ ] **Step 3: Translate the delta**

Submit only the new flat-string delta through Corvus Translate in UI mode for the catalog's existing non-English locales. Merge the completed output into the destination catalog without changing existing translations.

- [ ] **Step 4: Audit destination output**

Run the string-catalog verifier, correctness audit, placeholder checks, locale-coverage count, and long-dash audit. Read every new destination value and correct any wrong-sense or glossary leak through the translation workflow.

### Task 5: Verification and Private Checkpoint

**Files:**
- Verify all files changed by Tasks 1 through 4

**Interfaces:**
- Consumes: the completed feature

- [ ] **Step 1: Run automated verification**

Run `swift test`, `swift build`, `git diff --check`, the long-dash audit, and the project resource audit. Confirm zero test failures and no new resource concern.

- [ ] **Step 2: Run app-level verification**

Launch a development build, confirm all four categories render, toggle the menu bar item off and back on, and verify the main Settings switch remains reachable. Check keyboard traversal and accessibility labels. If macOS blocks visual or accessibility inspection, report that limitation explicitly.

- [ ] **Step 3: Inspect and checkpoint**

Inspect `git status --short`, stage only the named source, test, catalog, plan, and specification files, review the staged diff and credential scan, then commit to the private source branch. Do not push the public remote.
