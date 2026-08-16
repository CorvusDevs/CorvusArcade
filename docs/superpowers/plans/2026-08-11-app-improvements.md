# Corvus Arcade App Improvements Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Improve keyboard and VoiceOver access, light-theme readability, credential storage, and library restore safety without changing Corvus Arcade's visual identity.

**Architecture:** Preserve the existing SwiftUI views and theme tokens while replacing gesture-only interactions with semantic controls. Add a focused Keychain wrapper for the optional SteamGridDB key and split backup restore into inspect and apply phases so the interface can confirm changes and create a recovery backup before writing preferences.

**Tech Stack:** Swift 6, SwiftUI, AppKit, Security.framework, Swift Package Manager.

## Global Constraints

- Target macOS 14 or later.
- Add no third-party dependency.
- Preserve all existing user changes and untracked files.
- Add no new user-facing text without updating every locale in `Localizable.xcstrings`.
- User-facing text must contain no long dash characters.

---

### Task 1: Semantic custom controls

**Files:**
- Modify: `Sources/CorvusArcade/Views/ContentView.swift`
- Modify: `Sources/CorvusArcade/Views/LibraryHome.swift`
- Modify: `Sources/CorvusArcade/Views/CockpitControls.swift`
- Modify: `Sources/CorvusArcade/Views/SettingsView.swift`
- Modify: `Sources/CorvusArcade/Views/GameDetail.swift`
- Modify: `Sources/CorvusArcade/Views/ScreenshotViewer.swift`

**Interfaces:**
- Consumes: Existing bindings and selection actions.
- Produces: Native `Button` controls that preserve visual styling and expose selected state to accessibility.

- [x] Replace navigation and artwork `onTapGesture` actions with plain-style buttons.
- [x] Replace segmented and pill text gestures with buttons and selected-state accessibility traits.
- [x] Give image-only screenshot buttons descriptive accessibility labels.
- [x] Search the views again and justify any remaining `onTapGesture` usage.

### Task 2: Semantic theme contrast

**Files:**
- Modify: `Sources/CorvusArcade/Views/GameDetail.swift`
- Modify: `Sources/CorvusArcade/Views/ContentView.swift`

**Interfaces:**
- Consumes: Existing `Theme.ink`, `Theme.muted`, and palette selection.
- Produces: Description text that follows the active palette and theme tokens that react to Increase Contrast.

- [x] Replace the fixed pale game-description color with a semantic theme token.
- [x] Increase custom border and secondary-text separation when macOS Increase Contrast is enabled.
- [x] Build the shared theme implementation used by both dark and light palettes.

### Task 3: SteamGridDB Keychain storage

**Files:**
- Create: `Sources/CorvusArcade/Engine/SecureSettings.swift`
- Modify: `Sources/CorvusArcade/Engine/Artwork.swift`
- Modify: `Sources/CorvusArcade/Views/SettingsView.swift`

**Interfaces:**
- Produces: `SecureSettings.steamGridDBKey: String` backed by a generic-password Keychain item.
- Consumes: One-time legacy value from `UserDefaults.standard.string(forKey: "steamGridDBKey")`.

- [x] Implement Keychain read, write, delete, and one-time migration with explicit OSStatus handling.
- [x] Bind Settings through local view state and save changes through `SecureSettings`.
- [x] Make artwork lookup read from `SecureSettings`.
- [x] Confirm the legacy preference is removed only after a successful Keychain write.

### Task 4: Restore preview and recovery backup

**Files:**
- Modify: `Sources/CorvusArcade/Engine/LibraryBackup.swift`
- Modify: `Sources/CorvusArcade/Views/SettingsView.swift`
- Modify: `Package.swift`
- Create: `Tests/CorvusArcadeTests/LibraryBackupTests.swift`

**Interfaces:**
- Produces: `LibraryBackup.inspect()`, `LibraryBackup.preview(_:into:)`, `LibraryBackup.apply(_:into:)`, and `LibraryBackup.writeRecoveryBackup(games:)`.
- Consumes: A decoded `LibraryBackup.Payload` selected by the user.

- [x] Separate file selection and decoding from preference mutation.
- [x] Compute counts for new games, existing games, and settings before applying.
- [x] Show a confirmation alert summarizing the restore.
- [x] Write a timestamped recovery backup to Application Support before applying.
- [x] Add unit tests for merge and preview behavior.

### Task 5: Verification

**Files:**
- Verify all modified files and the plan.

- [x] Run `swift test` and confirm zero failures.
- [x] Run `swift build` and confirm a Debug build completes.
- [x] Run the long-dash audit and catalog-vector audit.
- [x] Inspect `git diff --check`, `git diff --stat`, and every changed file.
- [x] Confirm the original checkout still contains only the user's pre-existing untracked files.
