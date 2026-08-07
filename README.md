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

> [!NOTE]
> **What runs today.** Battle.net + Overwatch, DRM-free **GOG installers**, and games that ship as a plain archive are tested and working on Apple Silicon. Games protected by anti-cheat will not run, and DirectX 12-only titles are still unproven. Corvus Arcade checks a game against a community anti-cheat database and tells you before you install.

A game launcher that runs Windows games on Apple Silicon Macs, with no CrossOver subscription and no manual Wine setup. Corvus Arcade bundles its own redistributable engine, **Wine 11** with **DXMT** and Apple's **D3DMetal** (Game Porting Toolkit), and drives it from a native **SwiftUI** launcher. Pick a game, press Play. It is free, and it runs entirely on your Mac.

<div align="center">

<img src="https://corvusdevs.github.io/CorvusArcade/screenshots/launcher.png?v=050" width="820" alt="Corvus Arcade game page showing Overwatch with a live performance readout and readiness checks">

<em>The game page: one-click Play next to a live performance readout, with readiness checks that fix issues before you play.</em>

</div>


<p align="center">
  <img src="https://corvusdevs.github.io/CorvusArcade/screenshots/overwatch.png?v=041" width="820" alt="Overwatch running in a macOS window on Apple Silicon through Corvus Arcade">
  <br><em>Overwatch in a live match, running in a macOS window.</em>
</p>

<p align="center">
  <img src="https://corvusdevs.github.io/CorvusArcade/screenshots/brotato.png?v=041" width="820" alt="Brotato, installed from a GOG installer, running in a macOS window">
  <br><em>Brotato, installed straight from its GOG installer.</em>
</p>

## Contents

- [What runs today](#what-runs-today)
- [Features](#features)
- [What makes Corvus Arcade different](#what-makes-corvus-arcade-different)
- [Requirements](#requirements)
- [Built with](#built-with)
- [Privacy](#privacy)
- [More from CorvusDevs](#more-from-corvusdevs)

## What runs today

Corvus Arcade is developed in the open, and this is an honest account of what has been verified.

- **Tested targets:** Battle.net + Overwatch on Apple Silicon, and a DRM-free GOG installer added through **Add Game** (Brotato), installed and launched end to end.
- **Known not to work:** games with anti-cheat. DirectX 12-only titles and most launchers remain unproven. Heavier GOG titles have not been tried.
- **Reporting:** issues and requests are welcome on GitHub.

## Features

- **Live GPU and memory.** A GPU and memory gauge sits right next to Play, with a rolling graph while you play, so you can see how your Mac is holding up.
- **Readiness checks with quick fixes.** The game page flags things like tight memory and offers a one-tap fix before you start.
- **Your whole library at a glance.** A Home view shows each game with its live status, ready, playing, or needs setup.
- **One-click Overwatch.** A curated setup creates a clean container, installs Battle.net, and launches straight toward a match.
- **Your own engine, bundled.** Wine 11 with DXMT and Apple's D3DMetal ship inside the app. No CrossOver, no Homebrew, no terminal.
- **Backend picker.** Choose DXMT (open-source Metal D3D11) or D3DMetal (Apple's toolkit, D3D11 and D3D12), globally or per game.
- **GPTK 3 or GPTK 4.** Switch between the stable D3DMetal 3 and the newer D3DMetal 4 beta per game, with the framework swapped in at launch.
- **Alt-tab friendly.** A windowed mode runs the game inside a Mac window so you can Cmd-Tab to other apps mid-game.
- **Frame-rate cap, MetalFX, and an FPS overlay.** Tune performance and heat, with a live frames-per-second readout when you want it.
- **Screenshots while you play.** A shortcut captures the game window, not the whole screen, and the shots live in a viewer on the game's page with a gallery and arrow keys.
- **Eight colour themes.** Four dark and four light, chosen in Settings. The default is drawn from the app icon.
- **Favourites, search, and sorting.** Pin games to the top of the library and the sidebar, search by name, and sort by last played, name or size.
- **Your saves are protected.** Removing a game asks first, says how many save files are at stake, and offers to export them before anything is deleted.
- **Alt reaches your games.** Windows games receive the Alt key, which macOS otherwise keeps for typing special characters.
- **Rename any game.** Correct a name the store got wrong, or one taken from an archive filename.
- **Export and restore.** Save your game list, preferences and per-game settings to one small file, and restore them on another Mac.
- **Knows what a game renders with.** Reads the game's own binaries to report DirectX version and bitness, and warns when neither backend can run it.
- **Desktop shortcuts.** Create a double-clickable launcher for any game; it resolves through Corvus Arcade so it never goes stale.
- **Per-game overrides.** Backend, GPTK version, window mode, and frame cap can all be set for a single game.
- **First-run system check.** Confirms Rosetta 2, the bundled engine, and free disk space before you start, and installs Rosetta for you if needed.
- **Real setup progress.** Launcher downloads show a live progress bar with sizes and a forward-only time estimate.
- **Cover art for every game.** Games that are not on Steam take their artwork from the game's own program, on a cover coloured to match.
- **Repair a container.** Restores missing fonts and re-applies a game's settings, fixing blank menus and text without touching what you installed.
- **See what each game uses.** The library shows the disk space per game and the total across your whole library.
- **Container management.** Repair, reset, delete, clear temporary files, see disk usage, and open the container folder from the game's page.
- **Stays out of the way.** Keeps the display awake while you play, hides on launch if you like, and tells you if a game crashed with one click to the log.
- **Add your own games (experimental).** Point it at a Windows installer or `.exe`, and it builds a fresh container. You can also drag a program straight onto the library.
- **Native and private.** SwiftUI throughout, no account, no telemetry.
- **Signed and notarized by Apple.** Every release carries an Apple Developer ID signature and is notarized, so macOS verifies it on open and never shows an unidentified-developer warning. The disk image is stapled, so that works offline too.

## What makes Corvus Arcade different

🎮 **A launcher, not a toolkit.** No CrossOver license, no Whisky bottles to hand-configure, no Terminal. The engine is bundled and the setup is one button.

🧩 **Real per-game control.** DXMT vs D3DMetal, GPTK 3 vs GPTK 4, fullscreen vs alt-tab-able window, and a frame cap, all switchable per game.

🐦 **Built for Apple Silicon.** No Intel baggage. The engine and the launcher are tuned for M-series Macs and Apple's Metal-based Game Porting Toolkit.

🛡️ **Private and free.** No account, no analytics, no phone-home. GPTK ships under Apple's non-commercial terms, so Corvus Arcade is free.

## Requirements

- Apple Silicon Mac (M1 or newer)
- macOS 26 or later (26.4 for the GPTK 4 backend)
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
