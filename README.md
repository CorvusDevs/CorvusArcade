<div align="center">

<img src="https://corvusdevs.github.io/CorvusArcade/icon.png?v=8" width="200" height="200" alt="Corvus Arcade icon">

# Corvus Arcade

**Run Windows games on your Apple Silicon Mac**

<p>
  <img src="https://img.shields.io/github/v/release/CorvusDevs/CorvusArcade?style=flat-square&color=6D5AE6&label=release" alt="Latest release">
  <img src="https://img.shields.io/badge/macOS-26.0+-000000?style=flat-square&logo=apple&logoColor=white" alt="macOS 26.0+">
  <img src="https://img.shields.io/badge/Apple%20Silicon-only-444?style=flat-square" alt="Apple Silicon only">
  <img src="https://img.shields.io/badge/price-free-4CAF50?style=flat-square" alt="Free">
</p>

<p>
  <a href="https://github.com/CorvusDevs/CorvusArcade/releases/latest"><img src="https://img.shields.io/badge/%E2%AC%87%20Download%20for%20macOS-6D5AE6?style=for-the-badge&logoColor=white" alt="Download for macOS" height="44"></a>
</p>

<p>
  <a href="https://corvusdevs.github.io/CorvusArcade/">Website</a> ·
  <a href="https://github.com/CorvusDevs/CorvusArcade/releases">Releases</a> ·
  <a href="#what-makes-corvus-arcade-different">Why Corvus</a> ·
  <a href="#privacy">Privacy</a>
</p>

</div>

---

A game launcher that runs Windows games on Apple Silicon Macs, with no CrossOver subscription and no manual Wine setup. Corvus Arcade bundles its own redistributable engine, **Wine 11** with **DXMT** and Apple's **D3DMetal** (Game Porting Toolkit), and drives it from a native **SwiftUI** launcher. Pick a game, press Play. It is free, and it runs entirely on your Mac.

<div align="center">

<img src="https://corvusdevs.github.io/CorvusArcade/screenshots/launcher.webp?v=080" width="920" alt="Corvus Arcade 0.8.0 showing Overwatch, compatibility checks, screenshots, and live system readiness">

<em>Your Windows game library, presented as a native Mac launcher.</em>

</div>

<table>
  <tr>
    <td width="50%"><img src="https://corvusdevs.github.io/CorvusArcade/screenshots/quick-launch.webp?v=080" alt="Quick Launch searching games and launcher actions"></td>
    <td width="50%"><img src="https://corvusdevs.github.io/CorvusArcade/screenshots/game-tools.webp?v=080" alt="Per-game settings with Save Data Vault and Container Snapshots"></td>
  </tr>
  <tr>
    <td align="center"><em>Open games and actions instantly with Command-K.</em></td>
    <td align="center"><em>Protect saves and the full game container before changes.</em></td>
  </tr>
</table>

<p align="center">
  <img src="https://corvusdevs.github.io/CorvusArcade/screenshots/overwatch.webp?v=080" width="920" alt="Overwatch running in a macOS window on Apple Silicon through Corvus Arcade">
  <br><em>Overwatch in a live match, running in a macOS window.</em>
</p>

<p align="center">
  <img src="https://corvusdevs.github.io/CorvusArcade/screenshots/crown-siege.webp?v=133" width="920" alt="Crown Siege">
  <br><em>Crown Siege running on Apple Silicon.</em>
</p>

## Contents

- [Features](#features)
- [Install](#install)
- [What makes Corvus Arcade different](#what-makes-corvus-arcade-different)
- [Requirements](#requirements)
- [Built with](#built-with)
- [Privacy](#privacy)
- [More from CorvusDevs](#more-from-corvusdevs)

## Features

- **Live GPU and memory.** A GPU and memory gauge sits right next to Play, with a rolling graph while you play, so you can see how your Mac is holding up.
- **Readiness checks with quick fixes.** The game page flags things like tight memory and offers a one-tap fix before you start.
- **Your whole library at a glance.** A Home view shows each game with its live status, ready, playing, or needs setup.
- **One-click Overwatch.** A curated setup creates a clean container, installs Battle.net, and launches straight toward a match.
- **Your own engine, bundled.** Wine 11 with DXMT and Apple's D3DMetal ship inside the app. No CrossOver, no Homebrew, no terminal.
- **Backend picker.** Choose DXMT (open-source Metal D3D11) or D3DMetal (Apple's toolkit, D3D11 and D3D12), globally or per game.
- **Recall for Overwatch.** Recall is the first recommendation for an installed Overwatch game when its runtime and host checks pass. D3DMetal remains a safe alternative, and your saved backend choice is preserved.
- **Your saves are protected.** Removing a game asks first, says how many save files are at stake, and offers to export them before anything is deleted.
- **Add your own games (experimental).** Point it at a Windows installer or `.exe`, and it builds a fresh container. You can also drag a program straight onto the library.

<details>
<summary><strong>Complete feature list</strong></summary>

- GPTK 3 or GPTK 4 per game
- Windowed play with Command-Tab support
- Frame-rate cap, MetalFX, and FPS overlay
- In-game screenshots and gallery
- Eight colour themes
- Favourites, search, sorting, and custom names
- Library export and restore
- DirectX and bitness detection
- Desktop shortcuts
- Automatic Rosetta setup and readiness checks
- Live installer progress and multi-part installer validation
- Automatic cover art and custom artwork
- Container repair, cleanup, reset, and disk usage
- Automatic updates

</details>

## Install

1. Download the latest disk image from [GitHub Releases](https://github.com/CorvusDevs/CorvusArcade/releases/latest).
2. Open it and drag Corvus Arcade to Applications.
3. Launch Corvus Arcade, choose a game, and follow the guided setup.

Every release is signed with an Apple Developer ID and notarized by Apple.

## Recall for Overwatch

Corvus Arcade 1.5.0 adds an experimental backend integrating [Recall 1.3.0](https://github.com/AsherJN/recall/releases/tag/v1.3.0), using runtime `phase2-20261007.3`. Recall combines Wine, Rosetta and DXMT's Direct3D 11 to Metal translation with Overwatch-specific graphics and launch improvements. Corvus keeps the existing game files and uses a separate Recall settings profile.

For an installed Overwatch game, **Recall is the first recommended backend after validation; D3DMetal is the safe second choice**. Switching remains your decision. Keep **DirectX 11** selected in Overwatch's Video settings. Recall requires Apple Silicon, Rosetta, an available Metal GPU and macOS 26.5 or later; 16 GB RAM is recommended upstream.

In Overwatch's **Graphics** tab, choose Recall. Corvus discovers a compatible runtime already on your Mac and verifies its pinned manifest and files before importing it. If no verified runtime is found, open **Settings → Storage & Advanced** and import an existing compatible runtime folder. **This update does not bundle or download Recall's runtime.** See the [official Recall project](https://github.com/AsherJN/recall) for its distribution and requirements. The standard Corvus Wine runtime remains included and unchanged.

Recall options include Temporal MetalFX, sharpening, proxy detection and Game Mode bundle preparation where supported. **Your setting** shows the saved choice; **Last prepared** shows the previous profile configuration, rather than claiming a measured in-game effect. Check macOS separately to confirm Game Mode activation. Compatibility cautions remain visible, and complete inspected launch logs can be copied for troubleshooting.

### Performance: upstream results and Corvus validation

Recall's [published benchmark](https://github.com/AsherJN/recall/blob/v1.3.0/docs/PERFORMANCE.md) reports these results for a custom Overwatch match:

| Upstream measurement | CrossOver 26.3, default D3DMetal | Recall 1.0 |
| --- | ---: | ---: |
| Average FPS | 58.4 | 99.6 |
| 1% low FPS | 7.4 | 36.0 |
| Stutters over 50 ms per minute | 39.5 | 2.0 |

These are **upstream measurements**, using a plugged-in M1 Pro MacBook Pro with 16 GB RAM, macOS 26.6.2, an external 144 Hz display, fullscreen 1920×1080 Low, V-Sync and Dynamic Render Scale off. The custom match used Workshop 929PJ, with one run per app and different measured durations. They compare **Recall 1.0**, not this Corvus integration or Recall 1.3.0, and do not establish an advantage over every other backend or on every Mac. Recall attributes its performance work to graphics compression and scaling, frame pacing, shader-cache reuse and reduced Wine stalls; see its [architecture notes](https://github.com/AsherJN/recall/blob/main/docs/HOW-IT-WORKS.md).

Our integration validation includes one user-reported completed match, with Recall confirmed in the logs and a normal session exit. **Corvus has not measured FPS or frame times for that match.** System memory readings cover all open apps and cannot attribute pressure to an individual application.

## What makes Corvus Arcade different

🎮 **A launcher, not a toolkit.** No CrossOver license, no Whisky bottles to hand-configure, no Terminal. The engine is bundled and the setup is one button.

🧩 **Real per-game control.** DXMT vs D3DMetal, GPTK 3 vs GPTK 4, fullscreen vs alt-tab-able window, and a frame cap, all switchable per game.

🐦 **Built for Apple Silicon.** No Intel baggage. The engine and the launcher are tuned for M-series Macs and Apple's Metal-based Game Porting Toolkit.

🛡️ **Private and free.** No account, no analytics, no phone-home. GPTK ships under Apple's non-commercial terms, so Corvus Arcade is free.

## Requirements

- Apple Silicon Mac (M1 or newer)
- macOS 26 or later (26.4 for GPTK 4; 26.5 for Recall)
- Rosetta 2 (the app installs it for you if it is missing)
- Around 60 GB free for a Battle.net + Overwatch install
- Corvus Arcade checks a multi-part installer's files before anything runs, and says if one is missing or damaged.

## Built with

**SwiftUI** · **Wine 11** · **DXMT** · **Apple D3DMetal / Game Porting Toolkit** · **Rosetta 2** · **Sparkle**

## Privacy

No accounts, no analytics, no telemetry, no tracking. Corvus Arcade runs entirely on your Mac. It downloads the Battle.net installer directly from Blizzard and, optionally, cover art from public sources; nothing else leaves your machine.

## More from CorvusDevs

| | App | Description |
|---|-----|-------------|
| <img src="https://corvusdevs.github.io/icons/corvus-display.png" width="32"> | [Corvus Display](https://corvusdevs.github.io/CorvusDisplay/) | Brightness, color, and resolution for any Mac display |
| <img src="https://corvusdevs.github.io/icons/corvus-player.png" width="32"> | [Corvus Player](https://corvusdevs.github.io/Corvus-Player/) | The most powerful media player for macOS |
| <img src="https://corvusdevs.github.io/icons/ekual.png" width="32"> | [Ekual](https://corvusdevs.github.io/Ekual/) | Automatic loudness equalization for macOS |
| <img src="https://corvusdevs.github.io/icons/corvus-rss.png" width="32"> | [Corvus RSS Reader](https://corvusdevs.github.io/Corvus-RSS-Reader-For-Safari/) | Privacy-first RSS reader for Safari |
| <img src="https://corvusdevs.github.io/icons/purple-crow.png" width="32"> | [Purple Crow for Safari](https://corvusdevs.github.io/Purple-Crow-For-Safari/) | BTTV, FFZ & 7TV emotes plus 50+ Twitch features |

---

<div align="center">

<sub>Made with care by <a href="https://corvusdevs.github.io">CorvusDevs</a> · Not affiliated with Blizzard, Apple, or CrossOver</sub>

</div>
