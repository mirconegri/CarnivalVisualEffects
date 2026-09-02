# 🎭 Carnival Visual Effects

[![HTML](https://img.shields.io/badge/Language-HTML-orange?style=for-the-badge)](https://developer.mozilla.org/en-US/docs/Web/HTML)
[![JavaScript](https://img.shields.io/badge/Language-JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black)](https://developer.mozilla.org/en-US/docs/Web/JavaScript)
[![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)](LICENSE)

> 🌐 **<a href="https://carnivalvisualeffects.mirconegri.com" target="_blank">Visit the project website</a>**

A standalone, audio-reactive web visualizer built with HTML5 Canvas and vanilla JavaScript, designed to drive projection screens at live events synced to music in real time.

Originally built for a Carnival DJ set at a university dormitory. The constraint was tight: no time to learn a dedicated VJing tool, no budget for software licenses, and a need to run fullscreen on a laptop already handling audio output. A single self-contained HTML file opening in a browser tab solved all three problems simultaneously.

> **This repository ships two standalone versions.** They are not interchangeable — read [File Versions Explained](#file-versions-explained) before choosing which one to open.

## Table of Contents

- [Preview](#preview)
- [File Versions Explained](#file-versions-explained)
- [Features](#features)
- [Tech Stack](#tech-stack)
- [Design Decisions](#design-decisions)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
- [Usage](#usage)
- [Configuration and Environment](#configuration-and-environment)
- [Contributing](#contributing)
- [License](#license)

## Preview

<table align="center">
  <tr>
    <td align="center" width="50%" valign="top">
      <img src="gif/video1.gif" width="100%"><br><br>
      <b>Quantum Nodes & Liquid Blob</b><br>
      Interconnected audio-reactive nodes and beat-synced fluid dynamics
    </td>
    <td align="center" width="50%" valign="top">
      <img src="gif/video2.gif" width="100%"><br><br>
      <b>Oscilloscope & Audio Spark</b><br>
      Frequency-based waveform distortion with triggered particle sparks
    </td>
  </tr>
  <tr>
    <td align="center" width="50%" valign="top">
      <img src="gif/video3.gif" width="100%"><br><br>
      <b>Flowing Waves & Particle Storm</b><br>
      Phase-shifting sine waves rendered within a chaotic particle field
    </td>
    <td align="center" width="50%" valign="top">
      <img src="gif/video4.gif" width="100%"><br><br>
      <b>Fractal Sync</b><br>
      Recursive geometric structures scaling to bass frequencies
    </td>
  </tr>
</table>

*Previews captured from the extended version — see the comparison table below for what differs in the base version.*

## File Versions Explained

| | `pattern-drawer.html` (base) | `pattern-drawer_audio_sync_&_image.html` (extended) |
|---|---|---|
| Pattern count | 14 | 22 |
| Pattern rotation | Random order | Sequential, cycling in order |
| Logo overlay | Not present | Cycles through `image0.png`, `image1.png`, ... on each switch, with beat-reactive pulse |
| Manual BPM fallback | Not present | `+` / `-` keys adjust a simulated BPM when no microphone is available |
| Ultrawide fillers | Not present | Dynamic filler layer on several patterns for wide projection setups |
| On-screen status | Shows mic state and pattern name in debug mode | No on-screen text |
| Auto-rotation timing | Correctly respects `minutesPerPattern` (3 min default) | Hardcoded to rotate every 5 seconds regardless of mode — `minutesPerPattern` is defined but never read by the rotation check. Fix: replace the hardcoded `5000` in the `setInterval` call with `this.switchInterval` |

**Which version to use:** the extended file is the actively developed version and is recommended for live events, provided you are aware of the rotation-timing bug above. The base file is simpler and has correctly working timed rotation — use it as a reference if you want to build a lighter variant.

## Features

*Describes the extended version. See the table above for what the base version omits.*

- Audio-reactive engine — syncs visuals to live microphone input via the Web Audio API `AnalyserNode` (FFT-based bass detection drives beat pulses)
- 22 patterns — Oscilloscope, Flowing Waves, Particle Storm, Quantum Nodes, Liquid Blob, Vinyl Grooves, Neon DNA, Audio Sparks, and more
- Logo overlay — cycles through local image files on every pattern switch, with a hit-reactive glow effect
- Ultrawide and multi-screen support — dynamic filler layers reduce dead zones on wide projection setups
- Fallback BPM system — manual `+` / `-` BPM control when microphone access is unavailable
- Debug mode (`D` key) — cycles through every pattern for quick preview
- Zero dependencies — pure vanilla JS, no bundler, no build step

## Tech Stack

- **Core:** HTML5 Canvas (2D context), vanilla JavaScript
- **Audio:** Web Audio API (`AudioContext`, `AnalyserNode`, `getUserMedia`)
- **Build tooling:** none — static files, open directly or serve from any HTTP server

## Design Decisions

**Why vanilla JS instead of a VJing framework or Three.js?** The deployment target was a browser tab on a laptop already under CPU load from audio software. Every added dependency — module bundler, WebGL context, external library — is a potential failure point during a live set. Vanilla Canvas 2D with no build step means the file opens in under a second, works offline, and has no runtime dependencies that could fail to load.

**Why two separate HTML files instead of a single versioned file?** The base file (`pattern-drawer.html`) was the original version used at the event. The extended file was developed afterward with new patterns and the logo overlay system. Keeping them separate means the original working version is always available as a stable fallback if the extended version has issues — a live-event safety net more valuable than a clean repository structure.

**Why `getUserMedia` instead of `AudioContext.createMediaElementSource`?** The visualizer is designed to react to whatever is playing through the room — DJ software, Spotify, a stream — without requiring the audio to be routed through the browser. Microphone input captures the room's acoustic output regardless of source, which is the only approach that works when the audio chain is external to the browser.

## Project Structure

```
CarnivalVisualEffects/
├── pattern-drawer.html                      # Base version — 14 patterns, correct timed rotation
├── pattern-drawer_audio_sync_&_image.html   # Extended version — 22 patterns, logo, BPM, fillers
├── gif/
│   ├── video1.gif
│   ├── video2.gif
│   ├── video3.gif
│   └── video4.gif
├── README.md
└── LICENSE
```

> Optional user-provided assets for the extended version: `image0.png`, `image1.png`, etc. in the project root. These are not included in the repository — missing files are skipped silently and the overlay resets to `image0.png` on the next cycle.

## Getting Started

### Prerequisites

- Any modern browser (Chrome, Firefox, Edge, Safari)
- A local HTTP server — required for microphone access (browsers block `getUserMedia` on `file://` protocol)
- *(Extended version only, optional)* Logo image files (`image0.png`, `image1.png`, ...) in the project root

### Installation

```bash
git clone https://github.com/mirconegri/CarnivalVisualEffects.git
cd CarnivalVisualEffects
```

No build step or package installation required.

## Usage

Start a local server:

```bash
python3 -m http.server 8000
```

Open the version you want:

```
http://localhost:8000/pattern-drawer.html
http://localhost:8000/pattern-drawer_audio_sync_&_image.html
```

Click anywhere on the page to initialize the `AudioContext` and request microphone permission.

### Controls

| Key / Action | Effect |
|---|---|
| Click anywhere | Initialize audio context and request microphone access |
| `D` | Toggle debug mode — cycles patterns for preview |
| `N` | Skip to the next pattern immediately |
| `+` | Increase manual BPM *(extended version only, mic inactive)* |
| `-` | Decrease manual BPM *(extended version only, mic inactive)* |

## Configuration and Environment

No environment variables or `.env` file. All configuration is in each file's script constants:

| Constant | File | Purpose |
|---|---|---|
| `minutesPerPattern` | Both | Auto-rotation interval — respected in the base version, currently ignored in the extended version (see known issue above) |
| `this.bpm` | Extended only | Default fallback BPM when no microphone is active (default: `120`) |
| `image{N}.png` | Extended only | Logo image sequence — provide files in the project root to enable the overlay |

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/your-feature`)
3. Commit your changes with a clear message
4. Open a Pull Request

The rotation-timing bug in the extended version is a known good first issue. New pattern contributions are also welcome — each pattern is a self-contained method in the `Visualiser` class. Open an [Issue](https://github.com/mirconegri/CarnivalVisualEffects/issues) to discuss before implementing.

### Author

**Mirco Negri** — Computer Science @ UniTrento

[![Portfolio](https://img.shields.io/badge/Portfolio-00599C?style=for-the-badge&logo=globe&logoColor=white)](https://mirconegri.github.io/Portfolio/)
[![GitHub](https://img.shields.io/badge/GitHub-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/mirconegri)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/mirco-negri-263810225)
[![Gmail](https://img.shields.io/badge/Gmail-D14836?style=for-the-badge&logo=gmail&logoColor=white)](mailto:mirconegri06@gmail.com)

## License

This project is licensed under the MIT License — see the [LICENSE](LICENSE) file for details.
