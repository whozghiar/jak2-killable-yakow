# Interactive & Vulnerable Yakows

<p align="center">
  <img src="https://img.shields.io/badge/OpenGOAL-Mod-blue.svg" alt="OpenGOAL Mod">
  <img src="https://img.shields.io/badge/Game-Jak%202-orange.svg" alt="Game">
  <img src="https://img.shields.io/badge/AI--assisted-Modding-purple.svg" alt="AI Assisted">
</p>

---

> [!NOTE]
> This mod moved from the `jak2/features/killable_yakow` branch of [whozghiar/jak-project](https://github.com/whozghiar/jak-project) to this repository. Earlier releases stay installable from the launcher catalog.

## 📖 Overview
Makes the peaceful Yakow farm animals killable and protected by Krimzon law. Striking a Yakow immediately triggers a Krimzon Guard alert ("Hands off the cow!"), while defeating it drops dark eco pills with authentic death VFX.

- **Target Game:** Jak 2
- **Repository:** [`whozghiar/jak2-mod-killable-yakow`](https://github.com/whozghiar/jak2-mod-killable-yakow)

## ✨ Key Features
- **Feature:** Yakows are now vulnerable and killable, taking damage from player attacks.
- **Feature:** Striking a Yakow instantly triggers Krimzon Guard Alert Level 1 ("Hands off the cow!").
- **Feature:** Purple dissolution death VFX and drops 6 Dark Eco pills upon defeat.

## 📥 Download & Play via OpenGOAL Launcher (Players)

> [!TIP]
> **No developer environment required!** You can install and play this mod directly using the official OpenGOAL Launcher:

### Option A — Add Custom Mod Source (Recommended)
1. In the **OpenGOAL Launcher**, navigate to **Settings ▸ Mods ▸ Add Custom Mod Source**.
2. Paste this catalog URL:
   ```text
   https://raw.githubusercontent.com/whozghiar/jak2-mod-killable-yakow/main/index.json
   ```
3. Go to the **Mods** tab, locate **Killable Yakow**, and click **Install**.
4. Select your clean PS2 Jak II ISO when prompted. The launcher will automatically extract assets and launch the game!

### Option B — Manual Installation from GitHub Releases
1. Download the pre-built package for your operating system from the [Releases](https://github.com/whozghiar/jak-project/releases) tab (`windows-v*.zip` or `linux-v*.zip`).
2. Extract the archive into your OpenGOAL Launcher features directory:
   - **Windows:** `%APPDATA%\OpenGOAL-Launcher\features\jak2\mods\_local\yakow_killable\`
   - **Linux:** `~/.config/OpenGOAL-Launcher/features/jak2/mods/_local/yakow_killable/`
3. Launch the game from the OpenGOAL Launcher.

---

## 🛠️ Developer Setup & Local Compilation

### 1. Select the Active Game
Make sure your environment is targeting Jak 2:
```bash
task set-game-jak2
```

### 2. Binary Compilation
- **Status:** Layer 3 (GOAL only) — Not required if standard binaries already exist
- **Details:** Only GOAL scripts are modified. No C++ rebuild needed. For first-time build, use the fast targeted task:
```bash
task build-release-game
```

### 3. Asset Extraction
- **Status:** Standard extraction sufficient (once per setup)
- **Details:** Standard extraction sufficient. Uses native in-game models, animations, and sound effects.
```bash
task extract
```

### 4. Launch the Game
Run the game natively:
```bash
task boot-game
```
*(Or iterate fast via the OpenGOAL REPL using `task repl`, then hot-reload with `(mi)` and `(r)`).*

### 5. Enable the Mod (OFF by default)
This mod ships **disabled** — a fresh install leaves the farm Yakows as the stock invulnerable animals. Open the in-game Mods menu with **L3 + SELECT** (operates in retail boot, no debug mode required):
```text
Mods ▸ yakow-killable ▸ Enable
```

## 🎥 Demonstration Video

[![Demonstration Video](https://img.youtube.com/vi/njKxjCuEpcU/maxresdefault.jpg)](https://youtu.be/njKxjCuEpcU)

▶️ **[Watch the demonstration video on YouTube](https://youtu.be/njKxjCuEpcU)**

## 📖 Technical Documentation
For the complete technical breakdown, architecture, and developer notes, refer to:
- 📄 [`docs/modding/current_mod/yakow_killable_readme.md`](docs/modding/current_mod/yakow_killable_readme.md)
