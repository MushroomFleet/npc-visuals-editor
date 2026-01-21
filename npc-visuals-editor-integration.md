# NPC Visuals Editor - Integration Guide

This guide covers how to integrate the NPC Visuals Editor into your React project and adapt it for your own character roster and dialogue system requirements.

---

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Installation](#installation)
3. [Basic Integration](#basic-integration)
4. [Customizing the NPC Roster](#customizing-the-npc-roster)
5. [Understanding Asset Types](#understanding-asset-types)
6. [IndexedDB Storage](#indexeddb-storage)
7. [Export Formats](#export-formats)
8. [Using Baked Assets in Production](#using-baked-assets-in-production)
9. [Integration with RPGDialogue.jsx](#integration-with-rpgdialoguejsx)
10. [Styling & Theming](#styling--theming)
11. [API Reference](#api-reference)
12. [Troubleshooting](#troubleshooting)

---

## Prerequisites

- **React 18+** (uses hooks and modern React patterns)
- **Modern browser** with IndexedDB support (Chrome, Firefox, Safari, Edge)
- **ES6+ JavaScript** environment

No additional dependencies required - the component is self-contained with:
- Built-in IndexedDB manager
- Custom ZIP generation utility
- Inline styles (no external CSS required)

---

## Installation

### Option 1: Direct File Import

Copy `NPC-Visuals-Editor.jsx` to your project's components directory:

```
src/
  components/
    NPC-Visuals-Editor.jsx
```

Import into your application:

```jsx
import NPCVisualsEditor from './components/NPC-Visuals-Editor';

function App() {
  return <NPCVisualsEditor />;
}
```

### Option 2: Standalone HTML Demo

For quick testing without a build system, use `demo.html` which includes all component code inline with Babel transpilation.

Simply open `demo.html` in a modern browser - no server required.

---

## Basic Integration

### Minimal Setup

```jsx
import React from 'react';
import NPCVisualsEditor from './NPC-Visuals-Editor';

function CharacterEditorPage() {
  return (
    <div style={{ height: '100vh' }}>
      <NPCVisualsEditor />
    </div>
  );
}

export default CharacterEditorPage;
```

The editor is designed to fill its container. For best results, ensure the parent element has a defined height.

### With React Router

```jsx
import { BrowserRouter, Routes, Route } from 'react-router-dom';
import NPCVisualsEditor from './NPC-Visuals-Editor';

function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/editor" element={<NPCVisualsEditor />} />
        {/* Other routes */}
      </Routes>
    </BrowserRouter>
  );
}
```

---

## Customizing the NPC Roster

The `NPC_ROSTER` constant defines all characters and their asset requirements. Modify this object to match your game's character needs.

### Roster Structure

```javascript
const NPC_ROSTER = {
  character_id: {
    id: 'character_id',           // Unique identifier (used in filenames)
    name: 'Display Name',         // Short name for UI
    displayName: 'Full Title',    // Extended name with role description
    
    fullscreen: {                 // JRPG-style large portraits (or null)
      size: 512,                  // Recommended dimensions
      expressions: ['neutral', 'happy', 'angry', 'sad']  // Expression states
    },
    
    toast: {                      // StarFox-style small animated portraits (or null)
      size: 48,                   // Pixel art dimensions
      states: ['idle', 'talking', 'alarmed'],  // Animation states
      framesPerState: {           // Frames per animation
        idle: 2,
        talking: 4,
        alarmed: 2
      }
    },
    
    hud: {                        // Doom-style persistent pilot portrait (or null)
      size: 64,
      states: ['healthy', 'wounded', 'critical'],
      framesPerState: {
        healthy: 2,
        wounded: 2,
        critical: 3
      }
    }
  }
};
```

### Adding a New Character

```javascript
// Add to NPC_ROSTER object
merchant: {
  id: 'merchant',
  name: 'Merchant',
  displayName: '"The Wandering Trader"',
  fullscreen: {
    size: 512,
    expressions: ['neutral', 'greeting', 'haggling', 'pleased', 'disappointed']
  },
  toast: {
    size: 48,
    states: ['idle', 'talking'],
    framesPerState: { idle: 2, talking: 4 }
  },
  hud: null  // Merchant doesn't appear in HUD
}
```

### Character Without Certain Modes

Set any mode to `null` if that character doesn't use it:

```javascript
narrator: {
  id: 'narrator',
  name: 'Narrator',
  displayName: 'Story Narrator',
  fullscreen: {
    size: 512,
    expressions: ['default']
  },
  toast: null,  // No toast portrait
  hud: null     // No HUD portrait
}
```

---

## Understanding Asset Types

### Fullscreen Mode (JRPG Style)

**Purpose:** Pre-mission briefings, story cutscenes, dialogue scenes

**Specifications:**
- Resolution: 512×512 pixels (recommended)
- Format: PNG with transparency
- Style: Clean anime-adjacent artwork
- One image per expression state

**Naming Convention:**
```
portraits/{character_id}_fullscreen_{expression}.png

Examples:
portraits/viper_fullscreen_neutral.png
portraits/command_fullscreen_concerned.png
```

### Toast Mode (StarFox Style)

**Purpose:** In-game popup communications, objective updates, warnings

**Specifications:**
- Resolution: 48×48 pixels
- Format: PNG with transparency
- Style: Pixel art, no anti-aliasing
- Multiple frames per animation state

**Naming Convention:**
```
sprites/{character_id}_toast_{state}_{frame}.png

Examples:
sprites/viper_toast_talking_0.png
sprites/viper_toast_talking_1.png
sprites/viper_toast_talking_2.png
sprites/viper_toast_talking_3.png
```

### HUD Mode (Doom Face Style)

**Purpose:** Persistent pilot portrait showing player state

**Specifications:**
- Resolution: 64×64 pixels
- Format: PNG with transparency
- Style: Pixel art with helmet/visor
- State-based animations triggered by gameplay

**Naming Convention:**
```
hud/{character_id}_hud_{state}_{frame}.png

Examples:
hud/viper_hud_idle_healthy_0.png
hud/viper_hud_taking_damage_0.png
hud/viper_hud_victory_0.png
```

---

## IndexedDB Storage

The editor uses IndexedDB for client-side persistence during development sessions.

### Database Structure

```javascript
Database: 'NPCVisualsEditor'
Version: 1
Store: 'npc_images'

Record Schema:
{
  id: string,           // Key format: "{npcId}_{mode}_{state}_{frame}"
  imageData: string,    // Base64 encoded image data
  metadata: {
    npcId: string,
    mode: string,
    state: string,
    frame: number
  },
  timestamp: number     // Upload timestamp
}
```

### Accessing Data Programmatically

```javascript
// The dbManager is available globally in the component
// For external access, you can create a new instance:

const dbManager = new IndexedDBManager();
await dbManager.init();

// Get all images
const allImages = await dbManager.getAllImages();

// Get specific image
const image = await dbManager.getImage('viper_fullscreen_neutral_0');

// Save image
await dbManager.saveImage('custom_key', base64Data, metadata);

// Delete image
await dbManager.deleteImage('viper_fullscreen_neutral_0');

// Clear all data
await dbManager.clearAll();
```

---

## Export Formats

### JSON Manifest Export

The "Export JSON" button generates `npc-manifest.json`:

```json
{
  "version": "1.0.0",
  "generatedAt": "2025-01-21T10:30:00.000Z",
  "characters": {
    "viper": {
      "id": "viper",
      "name": "Viper",
      "displayName": "\"Viper\" (Player Pilot)",
      "assets": {
        "fullscreen": {
          "size": 512,
          "expressions": {
            "neutral": "portraits/viper_fullscreen_neutral.png",
            "determined": "portraits/viper_fullscreen_determined.png"
          }
        },
        "toast": {
          "size": 48,
          "states": {
            "idle": [
              "sprites/viper_toast_idle_0.png",
              "sprites/viper_toast_idle_1.png"
            ],
            "talking": [
              "sprites/viper_toast_talking_0.png",
              "sprites/viper_toast_talking_1.png",
              "sprites/viper_toast_talking_2.png",
              "sprites/viper_toast_talking_3.png"
            ]
          }
        },
        "hud": {
          "size": 64,
          "states": {
            "idle_healthy": ["hud/viper_hud_idle_healthy_0.png"]
          }
        }
      }
    }
  }
}
```

### BAKE ZIP Export

The "BAKE" button generates `npc-textures.zip` containing:

```
npc-textures.zip
├── npc-manifest.json
├── portraits/
│   ├── viper_fullscreen_neutral.png
│   ├── viper_fullscreen_determined.png
│   └── ...
├── sprites/
│   ├── viper_toast_idle_0.png
│   ├── viper_toast_idle_1.png
│   └── ...
└── hud/
    ├── viper_hud_idle_healthy_0.png
    └── ...
```

---

## Using Baked Assets in Production

### 1. Extract ZIP to Project

```
your-game/
├── public/
│   └── textures/
│       ├── npc-manifest.json
│       ├── portraits/
│       ├── sprites/
│       └── hud/
└── src/
```

### 2. Create Asset Loader

```javascript
// src/utils/npcAssets.js

let manifest = null;

export async function loadNPCManifest() {
  if (manifest) return manifest;
  
  const response = await fetch('/textures/npc-manifest.json');
  manifest = await response.json();
  return manifest;
}

export function getFullscreenPortrait(characterId, expression) {
  const char = manifest?.characters[characterId];
  const path = char?.assets?.fullscreen?.expressions?.[expression];
  return path ? `/textures/${path}` : null;
}

export function getToastFrames(characterId, state) {
  const char = manifest?.characters[characterId];
  const paths = char?.assets?.toast?.states?.[state];
  return paths ? paths.map(p => `/textures/${p}`) : [];
}

export function getHudFrames(characterId, state) {
  const char = manifest?.characters[characterId];
  const paths = char?.assets?.hud?.states?.[state];
  return paths ? paths.map(p => `/textures/${p}`) : [];
}
```

### 3. Preload Assets

```javascript
// src/utils/preloader.js

export async function preloadNPCAssets() {
  const manifest = await loadNPCManifest();
  const imagePaths = [];
  
  Object.values(manifest.characters).forEach(char => {
    // Collect all image paths
    if (char.assets.fullscreen) {
      Object.values(char.assets.fullscreen.expressions).forEach(p => {
        imagePaths.push(`/textures/${p}`);
      });
    }
    if (char.assets.toast) {
      Object.values(char.assets.toast.states).forEach(frames => {
        frames.forEach(p => imagePaths.push(`/textures/${p}`));
      });
    }
    if (char.assets.hud) {
      Object.values(char.assets.hud.states).forEach(frames => {
        frames.forEach(p => imagePaths.push(`/textures/${p}`));
      });
    }
  });
  
  // Preload all images
  await Promise.all(
    imagePaths.map(src => new Promise((resolve, reject) => {
      const img = new Image();
      img.onload = resolve;
      img.onerror = reject;
      img.src = src;
    }))
  );
}
```

---

## Integration with RPGDialogue.jsx

### Fullscreen Dialogue

```jsx
import RPGDialogue from './RPGDialogue';
import { getFullscreenPortrait } from './utils/npcAssets';

function MissionBriefing({ dialogue }) {
  return (
    <RPGDialogue
      mode="fullscreen"
      character={dialogue.character}
      text={dialogue.text}
      characterImage={getFullscreenPortrait(dialogue.characterId, dialogue.expression)}
      pose={dialogue.expression}
      visible={true}
      onComplete={() => advanceDialogue()}
      typewriterSpeed={25}
      showSkipHint={true}
    />
  );
}
```

### Toast Dialogue with Animation

```jsx
import RPGDialogue from './RPGDialogue';
import { getToastFrames } from './utils/npcAssets';

function InGameMessage({ message }) {
  const frames = getToastFrames(message.characterId, message.state);
  
  return (
    <RPGDialogue
      mode="toast"
      character={message.character}
      text={message.text}
      portraitFrames={frames}
      visible={true}
      onComplete={() => dismissMessage()}
      autoAdvance={true}
      autoAdvanceDelay={3000}
      position="bottom-left"
    />
  );
}
```

### Pilot HUD Component

```jsx
import { useState, useEffect } from 'react';
import { getHudFrames } from './utils/npcAssets';

function PilotHUD({ hp, maxHp, isFiring, missionState }) {
  const [frame, setFrame] = useState(0);
  
  // Determine state based on game conditions
  const getHudState = () => {
    if (missionState === 'victory') return 'victory';
    if (missionState === 'defeat') return 'defeat';
    
    const hpPercent = (hp / maxHp) * 100;
    if (hpPercent >= 75) return 'idle_healthy';
    if (hpPercent >= 50) return 'idle_wounded';
    if (hpPercent >= 25) return 'idle_critical';
    return 'idle_danger';
  };
  
  const state = getHudState();
  const frames = getHudFrames('viper', state);
  
  // Animate frames
  useEffect(() => {
    if (frames.length <= 1) return;
    
    const interval = setInterval(() => {
      setFrame(f => (f + 1) % frames.length);
    }, 500);
    
    return () => clearInterval(interval);
  }, [frames.length]);
  
  return (
    <div className="pilot-hud">
      <img 
        src={frames[frame] || '/textures/hud/placeholder.png'} 
        alt="Pilot"
        style={{ imageRendering: 'pixelated' }}
      />
    </div>
  );
}
```

---

## Styling & Theming

### Color Palette (CSS Variables)

```css
:root {
  --cyan-primary: #4a9eff;     /* Friendly/Player */
  --gold-primary: #ffd700;     /* Command/Priority */
  --orange-primary: #ff6b35;   /* Enemy intercept */
  --red-critical: #ff4444;     /* Emergency/Critical */
  --bg-dark: #0a0a12;
  --bg-panel: #0f0f1a;
  --bg-card: #1a1a2e;
  --text-primary: #ffffff;
  --text-secondary: #8892a0;
  --success-green: #00ff88;
}
```

### Customizing Border Colors by Transmission Type

```javascript
const BORDER_COLORS = {
  friendly: '#4a9eff',   // Cyan
  command: '#ffd700',    // Gold
  enemy: '#ff6b35',      // Orange
  emergency: '#ff4444',  // Red
};

// In your toast dialogue
<RPGDialogue
  mode="toast"
  // ... other props
  style={{
    '--toast-border-color': BORDER_COLORS[message.type]
  }}
/>
```

---

## API Reference

### NPCVisualsEditor Component

The main component has no required props - it's fully self-contained.

### IndexedDBManager Class

| Method | Parameters | Returns | Description |
|--------|------------|---------|-------------|
| `init()` | none | `Promise<IDBDatabase>` | Initialize database connection |
| `saveImage(key, imageData, metadata)` | key: string, imageData: string, metadata: object | `Promise` | Save image to store |
| `getImage(key)` | key: string | `Promise<object\|null>` | Retrieve single image |
| `getAllImages()` | none | `Promise<array>` | Get all stored images |
| `deleteImage(key)` | key: string | `Promise` | Remove single image |
| `clearAll()` | none | `Promise` | Clear all data |

### SimpleZip Class

| Method | Parameters | Returns | Description |
|--------|------------|---------|-------------|
| `addFile(name, data)` | name: string, data: string\|object | void | Add file to archive |
| `generate()` | none | `Blob` | Generate ZIP blob |

---

## Troubleshooting

### Images Not Persisting

**Cause:** IndexedDB blocked or cleared

**Solutions:**
1. Check browser privacy settings
2. Ensure not in private/incognito mode
3. Check storage quota: `navigator.storage.estimate()`

### ZIP Export Empty

**Cause:** No images uploaded yet

**Solution:** Upload at least one image before using BAKE

### Animation Not Playing

**Cause:** Only one frame uploaded for animation state

**Solution:** Upload multiple frames for animated states (toast/HUD)

### Portrait Not Transparent

**Cause:** Image saved as JPEG or has white background

**Solution:** Use PNG format with alpha channel transparency

### Preview Shows Placeholder

**Cause:** Image not yet uploaded for selected expression/state

**Solution:** 
1. Select the correct expression/state
2. Click upload button or asset slot
3. Choose transparent PNG image

---

## Support

For issues or feature requests, visit the [GitHub repository](https://github.com/MushroomFleet/npc-visuals-editor).
