<div align="center">

<img src="https://corvusdevs.github.io/CorvusArcade/icon.png" width="200" height="200" alt="Corvus Arcade icon">

# Corvus Arcade <sub><img src="https://img.shields.io/badge/status-ALPHA-E8B23C?style=flat-square" alt="Alpha"></sub>

**Run Windows games on your Apple Silicon Mac**

<p>
  <img src="https://img.shields.io/badge/status-alpha-E8B23C?style=flat-square" alt="Alpha status">
  <img src="https://img.shields.io/github/v/release/CorvusDevs/CorvusArcade?style=flat-square&color=6D5AE6&label=release" alt="Latest release">
  <img src="https://img.shields.io/badge/macOS-15.0+-000000?style=flat-square&logo=apple&logoColor=white" alt="macOS 15.0+">
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

> [!WARNING]
> **Corvus Arcade is early alpha.** So far it has only been built and tested with **Battle.net** and **Overwatch** on Apple Silicon. Other games and launchers are not supported yet and may not run at all. Expect rough edges, breakage between updates, and features that change or disappear. Use it for fun and experimentation, not as a dependable way to play.

A game launcher that runs Windows games on Apple Silicon Macs, with no CrossOver subscription and no manual Wine setup. Corvus Arcade bundles its own redistributable engine, **Wine 11** with **DXMT** and Apple's **D3DMetal** (Game Porting Toolkit), and drives it from a native **SwiftUI** launcher. Pick a game, press Play. It is free, and it runs entirely on your Mac.

<div align="center">

<img src="https://corvusdevs.github.io/CorvusArcade/screenshots/launcher.png" width="820" alt="Corvus Arcade game page showing Overwatch with a live performance readout and readiness checks">

<em>The game page: one-click Play next to a live performance readout, with readiness checks that fix issues before you play.</em>

</div>

## Contents

- [Alpha status](#alpha-status)
- [Features](#features)
- [What makes Corvus Arcade different](#what-makes-corvus-arcade-different)
- [Requirements](#requirements)
- [Built with](#built-with)
- [Privacy](#privacy)
- [More from CorvusDevs](#more-from-corvusdevs)

## Alpha status

Corvus Arcade is a work in progress and is shared in the open so people can follow along and try it.

- **Tested target:** Battle.net + Overwatch on Apple Silicon. That is the only combination that has been run end to end.
- **Everything else is unproven.** The "Add Game" flow is experimental. Anti-cheat, DirectX 12-only titles, and many launchers may not work at all.
- **No guarantees.** Things will break between builds. There is no support commitment yet.

## Features

- **Live performance readout.** A GPU and memory gauge sits right next to Play, with a rolling graph while you play, so you can see how your Mac is holding up.
- **Readiness checks with quick fixes.** The game page flags things like tight memory and offers a one-tap fix before you start.
- **Your whole library at a glance.** A Home view shows each game with its live status, ready, playing, or needs setup.
- **One-click Overwatch.** A curated setup creates a clean bottle, installs Battle.net, and launches straight toward a match.
- **Your own engine, bundled.** Wine 11 with DXMT and Apple's D3DMetal ship inside the app. No CrossOver, no Homebrew, no terminal.
- **Backend picker.** Choose DXMT (open-source Metal D3D11) or D3DMetal (Apple's toolkit, D3D11 and D3D12), globally or per game.
- **GPTK 3 or GPTK 4.** Switch between the stable D3DMetal 3 and the newer D3DMetal 4 beta per game, with the framework swapped in at launch.
- **Alt-tab friendly.** A windowed mode runs the game inside a Mac window so you can Cmd-Tab to other apps mid-game.
- **Frame-rate cap, MetalFX, and an FPS overlay.** Tune performance and heat, with a live frames-per-second readout when you want it.
- **Per-game overrides.** Backend, GPTK version, window mode, and frame cap can all be set for a single game.
- **First-run system check.** Confirms Rosetta 2, the bundled engine, and free disk space before you start, and installs Rosetta for you if needed.
- **Real setup progress.** The Battle.net download shows a live progress bar with a forward-only time estimate.
- **Bottle management.** Reset, delete, clear temporary files, see disk usage, and open the bottle folder from the game's page.
- **Stays out of the way.** Keeps the display awake while you play, hides on launch if you like, and tells you if a game crashed with one click to the log.
- **Add your own games (experimental).** Point it at a Windows installer or `.exe`, and it builds a fresh bottle.
- **Native and private.** SwiftUI throughout, no account, no telemetry.

## What makes Corvus Arcade different

🎮 **A launcher, not a toolkit.** No CrossOver license, no Whisky bottles to hand-configure, no Terminal. The engine is bundled and the setup is one button.

🧩 **Real per-game control.** DXMT vs D3DMetal, GPTK 3 vs GPTK 4, fullscreen vs alt-tab-able window, and a frame cap, all switchable per game.

🐦 **Built for Apple Silicon.** No Intel baggage. The engine and the launcher are tuned for M-series Macs and Apple's Metal-based Game Porting Toolkit.

🛡️ **Private and free.** No account, no analytics, no phone-home. GPTK ships under Apple's non-commercial terms, so Corvus Arcade is free.

## Requirements

- Apple Silicon Mac (M1 or newer)
- macOS 15 or later
- Rosetta 2 (the app installs it for you if it is missing)
- Around 60 GB free for a Battle.net + Overwatch install

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
