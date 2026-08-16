# Corvus Arcade Quick Launch Design

Date: 2026-08-11
Status: Approved

## Goal

Let people reach and launch any game quickly from anywhere inside Corvus Arcade without turning the app into a system-wide launcher or adding a background helper.

The feature should reduce navigation for larger libraries while preserving the app's current direct-launch, local-first behavior.

## Chosen Direction

Use a centered command palette presented as a temporary layer inside the main Corvus Arcade window.

The palette opens with Command-K from Home, a game detail page, or Settings. The current page remains visible under a subdued scrim so opening and closing the palette does not change navigation or lose context.

This is an app-only feature. Corvus Arcade must be active before Command-K works. The feature does not register a global shortcut, create another window, add a login item, or keep a helper process running.

## Presentation

- Center the palette in the detail area of the main window with a comfortable maximum width suitable for names, status, and shortcut hints.
- Use the active Corvus Arcade theme tokens for the surface, border, selection, text, and scrim.
- Keep the sidebar and current page recognizable behind the palette, but reduce their visual prominence while the palette is active.
- Place a focused search field at the top.
- Show matching results in a single keyboard-navigable list.
- Show game artwork when available and the existing themed fallback when it is not.
- Show the game name and one concise state line such as Running, Ready, or Not installed.
- Show the active graphics backend for ready games when space permits.
- Put keyboard guidance and secondary actions in a restrained footer.
- Avoid decorative animation. Use a short opacity and scale transition only when Reduce Motion is off.

## Opening and Closing

- Command-K opens the palette and moves keyboard focus into its search field.
- Invoking Command-K while the palette is already open keeps it open and returns focus to the search field.
- Escape closes the palette without changing the current sidebar selection.
- Clicking the scrim closes the palette.
- Choosing an action closes the palette before navigation, installation, or launch begins.
- Switching app language or theme while the palette is open may safely dismiss it as the root interface rebuilds.

## Search and Ordering

- An empty query shows all library games before utility actions.
- Favorite games come first, followed by the library's current stable order.
- Search matches game names case-insensitively and diacritic-insensitively.
- Matching happens locally against the in-memory library and never waits for artwork or network metadata.
- Add Game and Settings remain available as utility actions. They match their localized visible names and appear after game results.
- The first result is selected automatically when the query or result set changes.
- Up Arrow and Down Arrow move the selection without moving focus out of the search field.
- The list scrolls to keep the keyboard selection visible.
- If nothing matches, show a clear empty state and keep Add Game available as the useful next action.

## Game Actions

The default action reflects the game's actual state:

- Ready: Return launches the game through the existing `BottleManager.launch` path.
- Running: Return reveals the game detail page instead of starting a duplicate launch.
- Not installed: Return opens the game detail page at its existing installation action.
- Incomplete setup or missing executable: Return opens the game detail page so the existing repair or executable-selection flow remains authoritative.

Command-Return always opens the selected game's detail page without launching it. The visible shortcut hint must describe the action currently available rather than promising Launch for a game that cannot launch.

Quick Launch must not duplicate compatibility checks, memory warnings, crash handling, or launch progress state. It enters the same launch path as the existing Play button so all current safeguards remain in force.

## Utility Actions

- Add Game opens the existing Add Game sheet.
- Settings selects the existing in-window Settings destination.
- Utility actions use the same result-row navigation and Return behavior as games.
- Command-N and Command-Comma continue to work independently of Quick Launch.

## Architecture

- Keep palette presentation state at the `ContentView` level because that view owns sidebar navigation and the main-window overlay.
- Add an app command for Quick Launch in `CorvusArcadeApp`, using Command-K and the existing notification pattern used by Settings to request presentation in `ContentView`.
- Represent rows with a small identifiable action model that distinguishes a game, Add Game, and Settings while providing a localized title, state, artwork source, and executable action.
- Derive results from `BottleManager.games`, favorite state, bottle readiness, and running state. Do not create a second library cache.
- Route game navigation by assigning the existing `Sidebar.game` selection.
- Reuse the existing Add Game sheet and Settings selection rather than introducing parallel presentation state.
- Keep filtering synchronous because the expected data set is local and small. No task, timer, debounce, or persistent worker is needed.

## Accessibility and macOS Conventions

- Use a real text field and list semantics so VoiceOver announces the search field, result count, selected result, game state, and available action.
- Keep keyboard focus trapped within the temporary palette until it closes, without making the underlying controls appear disabled to assistive technology after dismissal.
- Provide accessibility labels that combine each game name with Running, Ready, Not installed, or the relevant repair state.
- Do not rely on artwork or status color alone.
- Support Full Keyboard Access, Return, Command-Return, arrow navigation, and Escape.
- With Reduce Motion enabled, use an opacity-only appearance with no scale transition.
- With Increase Contrast enabled, rely on the existing strengthened theme border behavior.
- Localize every visible label and accessibility phrase through the existing localization system.

## Error Handling and State Changes

- If the library is still loading, show a small loading state and keep Settings available.
- If a selected game disappears while the palette is open, recompute results and select the nearest valid row.
- If a game changes between ready, running, or incomplete while the palette is open, update its state and default action from `BottleManager` rather than retaining a stale closure.
- Existing `BottleManager.lastError`, launch warnings, and alerts remain responsible for reporting failures.
- Opening the palette must not dismiss an existing sheet, alert, or confirmation dialog. Command-K should be ignored while a modal presentation is active.

## Verification

- Build for the current macOS deployment target and run the existing test suite.
- Add focused tests for case-insensitive and diacritic-insensitive matching, favorite-first ordering, utility-action ordering, empty results, and selection recovery after result changes.
- Verify Command-K opens from Home, a game detail page, and Settings.
- Verify Command-K does not act while another sheet, alert, or confirmation is active.
- Verify Return launches a ready game through the existing launch path.
- Verify Return does not start a second copy of a running game.
- Verify uninstalled and incomplete games open their existing detail and setup paths.
- Verify Command-Return reveals a game without launching it.
- Verify Add Game and Settings reuse their current destinations.
- Verify Escape and scrim clicks dismiss without changing navigation.
- Verify keyboard focus, Full Keyboard Access, VoiceOver announcements, Reduce Motion, Increase Contrast, every app theme, and the supported localization set.
- Confirm filtering remains instant with a synthetic large library and adds no timers, network requests, persistent tasks, or meaningful idle resource use.

## Out of Scope

- A system-wide or global keyboard shortcut
- A floating utility window or menu bar replacement
- Background helpers, login items, or launch-at-login behavior
- Web search, store search, account integration, or network-backed suggestions
- New game installation, repair, compatibility, or launch logic
- Fuzzy matching beyond case and diacritic normalization
- Additional command categories, recent documents, or arbitrary settings search
