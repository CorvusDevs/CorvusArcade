# Settings Navigation and Menu Bar Visibility Design

Date: 2026-08-11
Status: Approved

## Goal

Make Settings easier to scan and navigate without introducing a second sidebar, and let people decide whether Corvus Arcade appears in the macOS menu bar.

## Chosen Direction

Use a native segmented category control at the top of Settings. This preserves the app's existing sidebar hierarchy and divides the current long page into four focused destinations.

The categories are:

1. General
   - Behavior
   - Appearance
   - Updates
   - Menu bar visibility
2. Graphics
   - Rendering options
   - Compatibility settings
3. Screenshots
   - Screenshot appearance
   - Screenshot save location
4. Storage & Advanced
   - Bottles
   - Artwork service
   - Library backup and restore
   - Diagnostics and advanced maintenance

## Interaction

- The category control remains visible at the top while the selected category's content scrolls below it.
- Standard SwiftUI controls provide keyboard navigation, focus behavior, and VoiceOver semantics.
- The selected category remains active while the Settings view remains alive.
- Switching categories does not modify any preference by itself.
- Existing settings keep their current defaults and persistence behavior.

## Menu Bar Visibility

- Add a General setting named "Show Corvus Arcade in the menu bar."
- The preference defaults to enabled so existing users retain current behavior.
- Changing the switch inserts or removes the menu bar extra immediately without relaunching the app.
- The setting remains available in the main app even when the menu bar extra is hidden, so the action is reversible.
- Store the preference with `AppStorage`, using one binding for both Settings and the `MenuBarExtra` scene.

The installed macOS SwiftUI interface exposes the `MenuBarExtra` initializer with an `isInserted` binding. A macOS 14 type-check also confirmed the exact title, system image, and binding combination used by this design.

## Architecture

- Keep `SettingsView` as the entry point.
- Add a small category enum that supplies stable identifiers and visible labels.
- Extract each category's content into a focused subview or private view builder so the existing section implementations can move without changing their behavior.
- Keep settings ownership where it currently lives unless a shared binding is required.
- Declare the menu bar preference at app scope with `AppStorage` and pass its binding to `MenuBarExtra` through `isInserted`.

## Error Handling and Safety

- Preserve the existing backup preview, recovery backup, and restore confirmation flow.
- Preserve existing alerts and status messages when sections move between categories.
- Hiding the menu bar item is not destructive and must not stop running games or background work.
- No migration is needed because the new preference has a safe default and no previous stored value.

## Accessibility and macOS Conventions

- Use a native segmented picker rather than custom clickable labels.
- Provide concise category labels that remain readable at the supported Settings width. Use "Screenshots" instead of the ambiguous "Captures" label so every locale names the image feature rather than an action.
- Retain logical focus order from the category selector into the active page.
- Do not rely on color alone to indicate the selected category.
- Keep controls grouped under visible section headings.

## Verification

- Build the app for its current macOS deployment target.
- Run the existing test suite.
- Add focused tests for the menu bar preference default and category definitions where they can be tested without UI automation.
- Verify each existing setting appears in exactly one category.
- Launch the app and confirm the menu bar item disappears and returns immediately.
- Confirm the switch remains accessible from the main app after hiding the menu bar item.
- Check keyboard traversal and VoiceOver labels for the category control.
- Re-run the resource audit and confirm the refactor does not add persistent work or meaningful memory overhead.

## Out of Scope

- Redesigning the visual style of individual setting rows
- Changing existing preference defaults other than adding the menu bar visibility default
- Reworking backup, restore, bottle, artwork, or screenshot behavior
- Adding new diagnostics or graphics features
