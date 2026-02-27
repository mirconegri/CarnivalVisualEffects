# 🎭 Carnival Visual Effects

[![HTML](https://img.shields.io/badge/Language-HTML-orange?style=for-the-badge)](https://developer.mozilla.org/en-US/docs/Web/HTML) [![JavaScript](https://img.shields.io/badge/Language-JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black)](https://developer.mozilla.org/en-US/docs/Web/JavaScript) [![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)](LICENSE)

I developed this standalone web visualizer to project audio-reactive effects on screens during the Carnival DJ set at my student dorm (the Nest). 
Built with **HTML5 Canvas** and **Vanilla JavaScript**, it uses the Web Audio API to draw 14 dynamic patterns perfectly synced to the music beat.

---

## 📸 Preview

<video src="images/video1.mp4" autoplay loop muted playsinline width="100%">aopo</video>

---

## 🚀 Features

- 🎤 **Audio-Reactive**: Syncs visuals to environmental audio via device microphone.
- 🎨 **14 Unique Patterns**: Spectrum Bars, Flowing Waves, Particle Storm, Kaleidoscope, and more.
- ⏱️ **Auto-Rotation**: Switches to a new random pattern every 3 minutes.
- ⚡ **Zero Dependencies**: Pure Vanilla JS and Canvas API in a single HTML file.

---

## 🧠 How It Works

The app connects to the **Web Audio API** to analyze frequency and beat data:
1. Open the file in a modern browser.
2. Click anywhere on the screen to initialize the audio context and grant microphone permissions.
3. The visualizer detects bass peaks to trigger beat-matched animations.

### ⌨️ Controls
- **Click**: Initialize microphone.
- **`N`**: Skip to the next random pattern.
- **`D`**: Toggle Debug Mode (displays pattern name and cycles them every 5 seconds).

---

## 🛠️ Tech Stack

- **HTML5 / CSS3**
- **Vanilla JavaScript**
- **Canvas 2D API** – rendering engine
- **Web Audio API** – audio spectrum analysis

---

## ⚙️ Installation

Clone the repository:
```bash
git clone [https://github.com/mirconegri/Carnival-Visual-Effects.git](https://github.com/mirconegri/Carnival-Visual-Effects.git)
cd Carnival-Visual-Effects

```

Run the application:

1. Double-click `pattern-drawer.html` to open it in your web browser.
2. Ensure you have a working microphone enabled.

*(Note: If running locally restricts microphone access due to browser security policies, serve the file using a simple local server like `python3 -m http.server 8000`)*

---

## 📜 License

MIT License © 2025 `Mirco Negri` — see [LICENSE](LICENSE) file for details.

---

## 👤 Author

`Mirco Negri` GitHub: https://github.com/mirconegri
