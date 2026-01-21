# 🎮 NPC Visuals Editor

**A React-based character asset management tool for RPG dialogue systems**

[![License: MIT](https://img.shields.io/badge/License-MIT-cyan.svg)](https://opensource.org/licenses/MIT)
[![React](https://img.shields.io/badge/React-18+-61DAFB?logo=react&logoColor=white)](https://reactjs.org/)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](http://makeapullrequest.com)

---

<p align="center">
  <img src="https://img.shields.io/badge/🖼️-Fullscreen_Portraits-ffd700?style=for-the-badge" alt="Fullscreen"/>
  <img src="https://img.shields.io/badge/💬-Toast_Animations-4a9eff?style=for-the-badge" alt="Toast"/>
  <img src="https://img.shields.io/badge/🎮-Pilot_HUD-ff6b35?style=for-the-badge" alt="HUD"/>
</p>

---

## Overview

NPC Visuals Editor is a comprehensive visual asset manager designed for games using RPG-style dialogue systems. It provides a streamlined workflow for uploading, previewing, and exporting character portraits across three distinct presentation modes:

| Mode | Style | Use Case |
|------|-------|----------|
| **Fullscreen** | JRPG-style large portraits | Story cutscenes, mission briefings, dialogue scenes |
| **Toast** | StarFox-style animated popups | In-game communications, objective updates, warnings |
| **Pilot HUD** | Doom-face style status portrait | Persistent player state indicator |

The editor stores assets client-side using **IndexedDB** during development, then exports production-ready **ZIP packages** with properly named files and a JSON manifest for seamless integration.

---

## ✨ Features

- **📤 Image Upload** — Drag-and-drop or click-to-upload with PNG/WebP/GIF transparency support
- **👁️ Live Preview** — Instantly see how portraits appear in each dialogue mode
- **🎬 Animation Playback** — Test animated sprite sequences for toast and HUD modes
- **💾 Persistent Storage** — IndexedDB keeps your work between sessions (no server required)
- **📊 Progress Tracking** — Visual indicators show completion status across all characters
- **📦 BAKE Export** — One-click ZIP generation with organized folder structure and JSON manifest
- **🎨 Cyberpunk UI** — TRON-inspired aesthetic with cyan/gold accent colors

---

## 🚀 Quick Start

### Option 1: Demo (No Setup Required)

Open **`demo.html`** directly in any modern browser. The demo is fully self-contained with all dependencies loaded via CDN.

```bash
# Just double-click demo.html or:
open demo.html
```

### Option 2: React Project Integration

1. Copy `NPC-Visuals-Editor.jsx` to your components folder
2. Import and render:

```jsx
import NPCVisualsEditor from './NPC-Visuals-Editor';

function App() {
  return <NPCVisualsEditor />;
}
```

---

## 📁 Project Structure

```
npc-visuals-editor/
├── NPC-Visuals-Editor.jsx          # Main React component
├── demo.html                        # Standalone demo (no build required)
├── npc-visuals-editor-integration.md  # Developer integration guide
├── README.md                        # This file
└── LICENSE                          # MIT License
```

---

## 🎭 Character Roster

The editor comes pre-configured with the R-STRIKER character roster:

| Character | Fullscreen | Toast | HUD |
|-----------|:----------:|:-----:|:---:|
| Viper (Player Pilot) | ✅ 6 expressions | ✅ 4 states | ✅ 11 states |
| Command (Mission Controller) | ✅ 4 expressions | ✅ 3 states | — |
| Intel (Intelligence Officer) | ✅ 4 expressions | ✅ 2 states | — |
| Rescued (Generic Personnel) | ✅ 3 expressions | ✅ 2 states | — |
| Enemy Commander (Antagonist) | ✅ 4 expressions | ✅ 2 states | — |
| Ground Team | — | ✅ 2 states | — |
| Enemy (Intercepted Comms) | — | ✅ 2 states | — |

Customize the roster by modifying the `NPC_ROSTER` constant. See the [Integration Guide](npc-visuals-editor-integration.md) for details.

---

## 📦 Export Format

### BAKE Output

Clicking **🔥 BAKE** generates `npc-textures.zip`:

```
npc-textures.zip
├── npc-manifest.json       # Asset mapping for all characters
├── portraits/              # 512×512 fullscreen images
│   ├── viper_fullscreen_neutral.png
│   ├── viper_fullscreen_determined.png
│   └── ...
├── sprites/                # 48×48 toast animation frames
│   ├── viper_toast_idle_0.png
│   ├── viper_toast_talking_0.png
│   └── ...
└── hud/                    # 64×64 pilot HUD frames
    ├── viper_hud_idle_healthy_0.png
    ├── viper_hud_taking_damage_0.png
    └── ...
```

### Manifest Structure

```json
{
  "version": "1.0.0",
  "generatedAt": "2025-01-21T10:30:00.000Z",
  "characters": {
    "viper": {
      "id": "viper",
      "name": "Viper",
      "assets": {
        "fullscreen": {
          "size": 512,
          "expressions": {
            "neutral": "portraits/viper_fullscreen_neutral.png"
          }
        },
        "toast": {
          "size": 48,
          "states": {
            "talking": ["sprites/viper_toast_talking_0.png", "..."]
          }
        },
        "hud": {
          "size": 64,
          "states": {
            "idle_healthy": ["hud/viper_hud_idle_healthy_0.png", "..."]
          }
        }
      }
    }
  }
}
```

---

## 🛠️ Integration Guide

For detailed instructions on:

- Customizing the NPC roster
- Asset specifications and naming conventions
- IndexedDB storage API
- Using baked assets in production
- Integration with RPGDialogue.jsx
- Styling and theming

See the complete **[Integration Guide](npc-visuals-editor-integration.md)**.

---

## 🎨 Asset Specifications

| Mode | Resolution | Format | Notes |
|------|------------|--------|-------|
| Fullscreen | 512×512 | PNG (transparent) | Clean anime-style artwork |
| Toast | 48×48 | PNG (transparent) | Pixel art, no anti-aliasing |
| HUD | 64×64 | PNG (transparent) | Pixel art with helmet/visor |

---

## 🔧 Browser Compatibility

| Browser | Supported |
|---------|:---------:|
| Chrome 80+ | ✅ |
| Firefox 75+ | ✅ |
| Safari 14+ | ✅ |
| Edge 80+ | ✅ |

Requires IndexedDB support. Not compatible with private/incognito mode in some browsers.

---

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## 📚 Citation

### Academic Citation

If you use this codebase in your research or project, please cite:

```bibtex
@software{npc_visuals_editor,
  title = {NPC Visuals Editor: Character Asset Management Tool for RPG Dialogue Systems},
  author = {Drift Johnson},
  year = {2025},
  url = {https://github.com/MushroomFleet/npc-visuals-editor},
  version = {1.0.0}
}
```

### Donate:

[![Ko-Fi](https://cdn.ko-fi.com/cdn/kofi3.png?v=3)](https://ko-fi.com/driftjohnson)

---

<p align="center">
  <sub>Built with ⚡ for game developers who believe characters deserve faces</sub>
</p>
