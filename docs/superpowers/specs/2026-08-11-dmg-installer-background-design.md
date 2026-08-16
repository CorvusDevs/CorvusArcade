# Corvus Arcade DMG Installer Background Design

Date: 2026-08-11
Status: Approved

## Goal

Make the macOS disk image immediately understandable and visually consistent with Corvus Arcade. The window must show the familiar drag-to-Applications direction without replacing Finder's real app and Applications items with decorative copies.

## Chosen Direction

Use the approved Iris route treatment. It combines a restrained Corvus violet background with a prominent solid arrow between the app and Applications positions.

The design should feel like a polished Mac installer first and branded artwork second. It must remain calm enough that the draggable items are the visual focus.

## Window Composition

- Keep the existing Finder window at 660 by 400 points.
- Keep the Corvus Arcade app at x 180, y 200.
- Keep the Applications link at x 480, y 200.
- Keep both Finder items at 96 points with 13 point labels.
- Center the arrow in the open space between the two items.
- Place the instruction "Drag Corvus Arcade to Applications" above the items, centered on the window.
- Leave generous clear space around both Finder labels and the window edges.

## Visual Treatment

- Use a deep charcoal-violet radial background with a subtle brighter iris behind the center path.
- Avoid texture, noise, mascots, screenshots, and decorative panels.
- Render the instruction in a restrained off-white system-style sans serif treatment.
- Render one solid horizontal violet arrow from left to right. Use rounded ends and a clear chevron head so the direction remains recognizable at a glance.
- Give the arrow a subtle lighter violet highlight. Avoid glow strong enough to compete with the app icon.
- Do not bake fake app icons, the Applications folder, or their labels into the background. Finder supplies the interactive items.

## Finder and Appearance Safety

- The background is a static 660 by 400 PNG at 72 DPI.
- Keep the zones behind Finder's item labels visually simple and high contrast.
- Mount a test disk image and inspect the real Finder labels, icons, arrow alignment, and instruction in both Light and Dark appearances.
- If Finder label contrast is insufficient in either appearance, adjust only the local background luminance behind the labels. Do not add duplicate baked labels.
- The artwork must not imply that the arrow itself is draggable or clickable.

## Build Integration

- Add a deterministic Swift and CoreGraphics generator for the background asset.
- Store the generated PNG in a tracked project asset location.
- Update `scripts/release.sh` to pass the asset to `create-dmg` with `--background`.
- Preserve the current window size, icon size, item coordinates, volume icon, compression format, signing, notarization, stapling, and Sparkle ZIP flow.
- Fail the DMG step clearly if the required background asset is missing or has incorrect dimensions. Do not silently ship the old empty layout.

## Accessibility and Clarity

- Direction must be communicated by shape and placement, not color alone.
- The instruction must use direct language and remain readable at the normal Finder window size.
- The visual path must unambiguously start at Corvus Arcade and end at Applications.
- The standard Finder items retain their normal drag, keyboard, VoiceOver, and context-menu behavior.

## Verification

- Verify the generated image is exactly 660 by 400 pixels at 72 DPI.
- Build a disposable unsigned test DMG without invoking the signed Release pipeline.
- Mount it in Finder and visually inspect the complete installer window.
- Confirm the real app can be dragged onto the real Applications link.
- Confirm labels are readable in Light and Dark appearances.
- Confirm the background, arrow, and icons remain aligned after closing and reopening the disk image.
- Run shell syntax validation on `scripts/release.sh`.
- Confirm the production release path still retains signing, notarization, and stapling behavior.

## Out of Scope

- Changing the app icon
- Changing the disk image size or the Finder item positions
- Adding a custom installer package or installer application
- Changing Sparkle, update signing, notarization credentials, or release publishing
- Publishing a new release as part of this design task
