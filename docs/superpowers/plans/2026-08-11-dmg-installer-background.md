# DMG Installer Background Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add the approved Iris route artwork to the Corvus Arcade macOS disk image and prove the mounted installer remains readable and functional.

**Architecture:** A deterministic Swift and CoreGraphics script generates one tracked 660 by 400 PNG. The release script validates that asset before passing it to `create-dmg`, while preserving the existing Finder item coordinates and signing flow. Verification uses a disposable unsigned app fixture so release signing and notarization are never invoked.

**Tech Stack:** Swift, AppKit, CoreGraphics, shell, `create-dmg 1.2.3`, `hdiutil`, Finder

## Global Constraints

- Keep the Finder window at 660 by 400 points.
- Keep Corvus Arcade at x 180, y 200 and Applications at x 480, y 200.
- Keep the icon size at 96 points and label size at 13 points.
- The background asset must be exactly 660 by 400 pixels at 72 DPI.
- Finder must supply the real app item, Applications link, and labels.
- Preserve volume icon, compression, signing, notarization, stapling, and Sparkle ZIP behavior.
- Do not publish or release as part of this work.

---

### Task 1: Deterministic Iris route background

**Files:**
- Create: `scripts/generate-dmg-background.swift`
- Create: `Resources/DMGBackground.png`

**Interfaces:**
- Consumes: an output path as the script's only command-line argument
- Produces: a 660 by 400 RGB PNG with 72 DPI metadata, charcoal-violet radial background, centered instruction, and solid right-facing arrow

- [ ] **Step 1: Implement the generator**

Create an `NSBitmapImageRep` at 660 by 400 pixels, draw through `NSGraphicsContext`, and write PNG data atomically to the output URL. Use these fixed composition values:

```swift
let canvasSize = NSSize(width: 660, height: 400)
let appCenter = NSPoint(x: 180, y: 200)
let applicationsCenter = NSPoint(x: 480, y: 200)
let instructionRect = NSRect(x: 80, y: 312, width: 500, height: 28)
let arrowStart = NSPoint(x: 270, y: 200)
let arrowEnd = NSPoint(x: 390, y: 200)
```

Draw the arrow as one rounded 8 point horizontal stroke ending in a 22 point chevron. Draw the instruction with the system font at 17 points, semibold, centered, in off-white. Keep the zones below each icon center free from ornament so Finder labels remain readable.

- [ ] **Step 2: Generate the tracked asset**

Run:

```bash
swift scripts/generate-dmg-background.swift Resources/DMGBackground.png
```

Expected: exit 0 and `Resources/DMGBackground.png` is created.

- [ ] **Step 3: Verify image dimensions and metadata**

Run:

```bash
sips -g pixelWidth -g pixelHeight -g dpiWidth -g dpiHeight Resources/DMGBackground.png
```

Expected: width 660, height 400, horizontal DPI 72, vertical DPI 72.

- [ ] **Step 4: Verify deterministic generation**

Generate a second copy in a temporary directory and compare bytes:

```bash
tmp_dir=$(mktemp -d /tmp/corvus-dmg-background.XXXXXX)
swift scripts/generate-dmg-background.swift "$tmp_dir/DMGBackground.png"
cmp Resources/DMGBackground.png "$tmp_dir/DMGBackground.png"
```

Expected: `cmp` exits 0.

- [ ] **Step 5: Commit the generator and asset**

```bash
git add scripts/generate-dmg-background.swift Resources/DMGBackground.png
git commit -m "Add Corvus Arcade DMG background"
```

### Task 2: Release integration and validation

**Files:**
- Modify: `scripts/release.sh:79-105`

**Interfaces:**
- Consumes: `Resources/DMGBackground.png`
- Produces: the existing release DMG with `--background "$DMG_BACKGROUND"`

- [ ] **Step 1: Add the background contract to the DMG block**

Define the asset next to `DMG`:

```bash
DMG_BACKGROUND="$ROOT/Resources/DMGBackground.png"
```

Before staging the app, require the file and validate dimensions with `sips`. On failure, print the missing or invalid path and exit 1. Read `pixelWidth` and `pixelHeight` separately and require `660` and `400` exactly.

- [ ] **Step 2: Pass the background to create-dmg**

Add this argument without changing the existing coordinates:

```bash
--background "$DMG_BACKGROUND" \
```

- [ ] **Step 3: Preserve temporary-directory cleanup on DMG failure**

Capture the `create-dmg` exit status, remove only the exact `mktemp -d` staging directory, then exit with an explanatory error instead of continuing with `|| true`. This prevents a release from silently omitting the required installer artwork.

- [ ] **Step 4: Validate the shell script**

Run:

```bash
bash -n scripts/release.sh
```

Expected: exit 0 and no output.

- [ ] **Step 5: Inspect the focused diff**

Run:

```bash
git diff -- scripts/release.sh
```

Expected: only background validation, the `--background` argument, and failure-safe staging cleanup change in the DMG section. Signing, notarization, stapling, Sparkle signing, and publishing remain unchanged.

- [ ] **Step 6: Commit release integration**

```bash
git add scripts/release.sh
git commit -m "Use branded background in release DMG"
```

### Task 3: Mounted DMG acceptance test

**Files:**
- Verify: `Resources/DMGBackground.png`
- Verify: `scripts/release.sh`

**Interfaces:**
- Consumes: generated background and the same `create-dmg` geometry as the release script
- Produces: disposable unsigned DMG evidence without invoking a Release build, signing, notarization, or publishing

- [ ] **Step 1: Create a disposable app fixture**

Create a temporary directory with a minimal `Corvus Arcade.app/Contents/MacOS` structure and an executable placeholder. Copy the background into the fixture through the normal `create-dmg --background` option.

- [ ] **Step 2: Build the unsigned test DMG**

Run `create-dmg` with window size 660 by 400, app coordinates 180 by 200, Applications coordinates 480 by 200, icon size 96, and text size 13. Do not invoke `scripts/release.sh` because that path builds and signs a production artifact.

- [ ] **Step 3: Mount and inspect the artifact**

Mount the resulting DMG read-only. Verify:

```bash
test -f "/Volumes/Corvus Arcade Test/.DS_Store"
test -f "/Volumes/Corvus Arcade Test/.background/DMGBackground.png"
test "$(readlink "/Volumes/Corvus Arcade Test/Applications")" = "/Applications"
test -d "/Volumes/Corvus Arcade Test/Corvus Arcade.app"
```

Expected: every check exits 0.

- [ ] **Step 4: Inspect the real Finder window visually**

Open the mounted volume and capture the complete Finder window. Confirm the real icons sit at each end of the arrow, the title is unobstructed, labels remain legible, and no fake icons or duplicate labels are baked into the background. Repeat the contrast inspection in Light and Dark appearances if the Finder labels differ.

- [ ] **Step 5: Detach the fixture and run project verification**

Detach only the mounted test volume, then run:

```bash
swift test
bash -n scripts/release.sh
git diff --check
```

Expected: all tests pass, shell syntax is valid, and no whitespace errors are reported.

- [ ] **Step 6: Audit repository state**

Run:

```bash
git status --short
git log -3 --oneline
```

Expected: only pre-existing untracked user files remain, and the two implementation commits appear after the design and plan checkpoints.
