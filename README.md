# 🎭 Carnival Visual Effects

[![HTML](https://img.shields.io/badge/Language-HTML-orange?style=for-the-badge)](https://developer.mozilla.org/en-US/docs/Web/HTML)
[![JavaScript](https://img.shields.io/badge/Language-JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black)](https://developer.mozilla.org/en-US/docs/Web/JavaScript)
[![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)](LICENSE)

A standalone, audio-reactive web visualizer built with HTML5 Canvas and vanilla JavaScript, designed to project dynamic visual patterns synced to live music on a screen — originally built for a Carnival DJ set at the author's student dorm.

> **This repository ships two standalone versions of the visualizer.** They are not interchangeable — see [File Versions Explained](#file-versions-explained) below before choosing which one to run.

## Table of Contents

- [Preview](#preview)
- [File Versions Explained](#file-versions-explained)
- [Features](#features)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
- [Usage](#usage)
- [Configuration and Environment](#configuration-and-environment)
- [Contributing](#contributing)
- [License](#license)

## Preview

<table align="center" style="border: none;">
  <tr>
    <td align="center" width="50%" valign="top">
      <img src="gif/video1.gif" width="100%" style="max-width: 400px; border-radius: 8px;"><br><br>
      <b>Quantum Nodes & Liquid Blob</b><br>
      Interconnected audio-reactive nodes and beat-synced fluid dynamics
    </td>
    <td align="center" width="50%" valign="top">
      <img src="gif/video2.gif" width="100%" style="max-width: 400px; border-radius: 8px;"><br><br>
      <b>Oscilloscope & Audio Spark</b><br>
      Frequency-based waveform distortion with triggered particle sparks
    </td>
  </tr>
  <tr>
    <td align="center" width="50%" valign="top">
      <img src="gif/video3.gif" width="100%" style="max-width: 400px; border-radius: 8px;"><br><br>
      <b>Flowing Waves & Particle Storm</b><br>
      Phase-shifting sine waves rendered within a chaotic particle field
    </td>
    <td align="center" width="50%" valign="top">
      <img src="gif/video4.gif" width="100%" style="max-width: 400px; border-radius: 8px;"><br><br>
      <b>Fractal Sync</b><br>
      Recursive geometric fractal structures scaling to bass frequencies
    </td>
  </tr>
</table>

*(Previews above were captured from the extended version — see below for what differs in the base version.)*

## File Versions Explained

The repository contains two independent, fully self-contained HTML files. Both implement the same core audio-reactive engine, but they diverge in scope:

| | `pattern-drawer.html` (base) | `pattern-drawer_audio_sync_&_image.html` (extended) |
|---|---|---|
| **Pattern count** | 14 | 22 (adds Starfield Warp, Equalizer Circle, Waveform Terrain, Quantum Nodes, Liquid Blob, Vinyl Grooves, Neon DNA, Audio Sparks) |
| **Pattern switching (auto)** | Random pattern each rotation | Sequential, cycling through the list in order |
| **Logo overlay** | Not present | Cycles through local `image0.png`, `image1.png`, ... on every pattern switch, rendered centered with a hit-reactive pulse |
| **Manual BPM fallback** | Not present | `+` / `-` keys adjust a simulated BPM (default 120) when no microphone is active |
| **Multi-screen / ultrawide fillers** | Not present | Several patterns render an additional dynamic filler layer for wide projection setups |
| **On-screen status text** | Shows "Click for Mic", "MIC ACTIVE", "MIC ERROR / DENIED", and the active pattern name in debug mode | No on-screen text feedback at all — mic status and pattern names are not displayed |
| **Auto-rotation timing** | Correctly respects `minutesPerPattern` (3 min) for normal rotation and a separate 5s interval in debug mode | ⚠️ Hardcoded to rotate every 5 seconds regardless of mode — the `minutesPerPattern` / debug interval settings exist in the code but are not actually used by the rotation check |

**Which one should you use?** The extended file is the actively developed version referenced by this README's feature list and is recommended for real events, provided you're aware of the 5-second rotation bug noted above. The base file is simpler, has correctly working timed rotation, and is a good reference if you want to build a lighter-weight variant without the logo/BPM/filler systems.

## Features

*(Describes the extended version, `pattern-drawer_audio_sync_&_image.html` — see the comparison table above for what the base version omits.)*

- **Audio-Reactive Engine:** syncs visuals to live microphone input via the Web Audio API's `AnalyserNode` (FFT-based bass detection triggers beat pulses)
- **22 Unique Patterns:** from Oscilloscopes and Flowing Waves to Quantum Nodes, Liquid Blobs, Vinyl Grooves, Neon DNA, and Audio Sparks
- **Logo Overlay:** cycles through local `image0.png`, `image1.png`, ... files (if present) and renders one centered over the active pattern on every switch, with a hit-reactive pulse/glow effect
- **Ultrawide & Multi-Screen Support:** several patterns render an additional dynamic filler layer (`renderDynamicFiller`) to reduce dead zones on wide projection setups
- **Fallback BPM System:** when microphone access is unavailable, an on-screen HUD lets you manually adjust a simulated BPM (`+` / `-`) instead of relying on live audio
- **Debug Mode:** press `D` to cycle through every pattern — see the [File Versions Explained](#file-versions-explained) table for the current rotation-timing caveat
- **Auto-Rotation:** switches to a new pattern automatically (intended to be configurable via `minutesPerPattern`, default 3 minutes — see known issue above)
- **Zero Dependencies:** pure vanilla JS, relying entirely on native Web APIs — no build step

## Tech Stack

- **Core:** HTML5 Canvas (2D context), vanilla JavaScript
- **Audio:** Web Audio API (`AudioContext`, `AnalyserNode`, `getUserMedia`)
- **Build tooling:** none — static files, no bundler or package manager

## Project Structure

```
CarnivalVisualEffects/
├── pattern-drawer.html                        # Base version — 14 patterns, random rotation
├── pattern-drawer_audio_sync_&_image.html      # Extended version — 22 patterns, logo + BPM + fillers
├── gif/
│   ├── video1.gif
│   ├── video2.gif
│   ├── video3.gif
│   └── video4.gif
├── README.md
└── LICENSE
```

> Optional, user-provided assets for the extended version: `image0.png`, `image1.png`, etc. in the project root, used by the logo-overlay feature. Not included in the repository — missing files are skipped silently.

## Getting Started

### Prerequisites

- Any modern web browser (Chrome, Firefox, Edge, Safari)
- A local web server for testing (see Security Note below)
- *(Optional, extended version only)* `image0.png`, `image1.png`, etc. in the project root if you want the logo-overlay feature to display something other than a broken image

### Installation

```bash
git clone https://github.com/mirconegri/CarnivalVisualEffects.git
cd CarnivalVisualEffects
```

No build step or package installation is required.

## Usage

1. Serve the project directory with a local web server — most browsers block microphone access on the `file://` protocol:

```bash
python3 -m http.server 8000
```

2. Open the version you want in your browser:

```
http://localhost:8000/pattern-drawer.html
```

or

```
http://localhost:8000/pattern-drawer_audio_sync_&_image.html
```

3. Click anywhere on the page to initialize the `AudioContext` and grant microphone permission — this activates real-time audio reactivity.

### Controls

| Key / Action | Effect |
|---|---|
| **Click anywhere** | Initialize `AudioContext` and request microphone access |
| **`D`** | Toggle debug mode (cycles through patterns; see rotation-timing note above) |
| **`N`** | Skip to the next pattern immediately |
| **`+`** | *(Extended version only)* Increase manual fallback BPM (only when mic is inactive) |
| **`-`** | *(Extended version only)* Decrease manual fallback BPM (only when mic is inactive) |

## Configuration and Environment

No environment variables or `.env` file are used. Configuration is done directly in each file's script constants:

| Constant | File(s) | Purpose |
|---|---|---|
| `minutesPerPattern` | Both | Intended auto-rotation interval — correctly used in the base version; not actually applied in the extended version (see known issue) |
| `this.bpm` | Extended only | Default fallback BPM when no microphone is active (default: `120`) |
| `image{N}.png` | Extended only | Optional logo image sequence for the overlay feature; missing files are silently skipped and cycling resets to `image0.png` |

⚠️ **Security Note:** Most modern browsers block microphone access on `file://` protocols. Always serve the directory via a local web server (e.g. `python3 -m http.server 8000` or VS Code Live Server) for audio-reactive features to work.

## Contributing

Contributions are welcome! To propose a change:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/your-feature`)
3. Commit your changes with a clear message
4. Open a Pull Request

Found a bug (including the rotation-timing issue in the extended version) or have a new pattern idea? Open an [Issue](https://github.com/mirconegri/CarnivalVisualEffects/issues).

### 👤 Author & Connect

**Mirco Negri** — *Computer Science Student @ UniTrento*

[![Portfolio](https://img.shields.io/badge/Portfolio-00599C?style=for-the-badge&logo=globe&logoColor=white)](https://mirconegri.github.io/Portfolio/)
[![GitHub](https://img.shields.io/badge/GitHub-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/mirconegri)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/mirco-negri-263810225)
[![Gmail](https://img.shields.io/badge/Gmail-D14836?style=for-the-badge&logo=gmail&logoColor=white)](mailto:mirconegri06@gmail.com)
[![Instagram](https://img.shields.io/badge/Instagram-E4405F?style=for-the-badge&logo=instagram&logoColor=white)](https://www.instagram.com/mirco_negri_?igsh=MWtlbXY0a3R4NTJmNA==)
[![Facebook](https://img.shields.io/badge/Facebook-1877F2?style=for-the-badge&logo=facebook&logoColor=white)](https://www.facebook.com/share/172rhaPCUK/)

## License

This project is licensed under the **MIT License** — see the [LICENSE](LICENSE) file for details.
<br>
© 2026 Mirco Negri
