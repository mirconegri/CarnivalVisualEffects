# 🎭 Carnival Visual Effects

[![HTML](https://img.shields.io/badge/Language-HTML-orange?style=for-the-badge)](https://developer.mozilla.org/en-US/docs/Web/HTML) [![JavaScript](https://img.shields.io/badge/Language-JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black)](https://developer.mozilla.org/en-US/docs/Web/JavaScript) [![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)](LICENSE)

I developed this standalone web visualizer to project `audio-reactive` effects on screens during the Carnival DJ set at my student dorm.
Built strictly with **HTML5 Canvas** and **Vanilla JavaScript**, it uses the Web Audio API to draw over 20 dynamic patterns perfectly synced to the music beat.

---


<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width">
  <style>
    * { margin: 0; padding: 0;}
    html, body { width: 100%; height: 100%; overflow: hidden; background: black; }
    #visualiser { position:absolute; width:100%; height:100%; display: block; }
    #debug-name { color: white; position: absolute; top: 20px; left: 20px; font-size: 30px; z-index: 10; font-family: monospace; }
  </style>
</head>
<body>
  <canvas id="visualiser"></canvas>
  <p id="debug-name"></p>
  <script>
    class Visualiser {
        constructor(canvasId) {
            this.canvas = document.getElementById(canvasId);
            this.ctx = this.canvas.getContext('2d');
            
            this.minutesPerPattern = 3;
            this.switchInterval = this.minutesPerPattern * 60 * 1000;
            this.startTime = Date.now();
            
            this.beat = 0;
            this.hit = 0;
            
            this.patterns = [
                {name: "Oscilloscope Sync", fun: this.renderOscilloscope.bind(this)},
                {name: "Fractal Sync", fun: this.renderFractalPulse.bind(this)},
                {name: "SpectrumBars Sync", fun: this.renderSpectrumBars.bind(this)},
                {name: "FlowingWaves Sync", fun: this.renderFlowingWaves.bind(this)},
                {name: "ParticleStorm Sync", fun: this.renderParticleStorm.bind(this)},
                {name: "SpiralWaves Sync", fun: this.renderSpiralWaves.bind(this)},
                {name: "Kaleidoscope Sync", fun: this.renderKaleidoscope.bind(this)},
                {name: "MatrixRain Sync", fun: this.renderMatrixRain.bind(this)},
                {name: "CircularPulse Sync", fun: this.renderCircularPulse.bind(this)},
                {name: "LaserBeams Sync", fun: this.renderLaserBeams.bind(this)},
                {name: "Confetti Sync", fun: this.renderConfetti.bind(this)},
                {name: "RadialBass Sync", fun: this.renderRadialBass.bind(this)},
                {name: "Tunnel Sync", fun: this.renderTunnel.bind(this)},
                {name: "HousePulseRings Sync", fun: this.renderHousePulseRings.bind(this)}
            ];
            
            this.currentPattern = null;
            this.patternData = {};
            this.debugMode = false;
            this.debugIndex = 0;
            this.debugInterval = 5000;
            
            this.resizeCanvas();
            window.addEventListener('resize', () => this.resizeCanvas());
            this.switchPattern();

            this.audioInit = false;
            document.addEventListener('click', () => this.initAudio(), {once:true});
        }

        async initAudio() {
            try {
                const stream = await navigator.mediaDevices.getUserMedia({ audio: true });
                this.actx = new (window.AudioContext || window.webkitAudioContext)();
                this.analyser = this.actx.createAnalyser();
                this.analyser.fftSize = 128; 
                this.actx.createMediaStreamSource(stream).connect(this.analyser);
                this.data = new Uint8Array(this.analyser.frequencyBinCount);
                this.lastBeatT = 0;
                this.audioInit = true;
                // Feedback visivo attivazione mic
                document.getElementById("debug-name").innerText = "MIC ACTIVE";
                setTimeout(() => { if(!this.debugMode) document.getElementById("debug-name").innerText = ''; }, 2000);
            } catch(e) {
                console.error("Mic error:", e);
                document.getElementById("debug-name").innerText = "MIC ERROR / DENIED";
            }
        }

        resizeCanvas() {
            const dpr = window.devicePixelRatio || 1;
            const displaySizes = this.canvas.getBoundingClientRect();
            this.canvas.width = displaySizes.width * dpr;
            this.canvas.height = displaySizes.height * dpr;
            this.ctx.scale(dpr, dpr);
            this.width = displaySizes.width;
            this.height = displaySizes.height;
            this.patternData = {};
        }

        generateWaveData(count = 128) {
            const data = [];
            for (let i = 0; i < count; i++) {
                const freq1 = Math.sin(i * 0.1 + this.beat * Math.PI) * 0.5;
                const freq2 = Math.sin(i * 0.05 + this.beat * 2) * 0.3;
                data.push((freq1 + freq2) * (0.5 + this.hit));
            }
            return data;
        }

        generateSpectrumData(count = 64) {
            const data = [];
            for (let i = 0; i < count; i++) {
                const val = Math.abs(Math.sin(this.beat * Math.PI + i * 0.2)) * (1 - i / count * 0.5);
                data.push(val * (0.3 + this.hit * 0.7));
            }
            return data;
        }

        clear(alpha) {
            this.ctx.fillStyle = `rgba(0, 0, 0, ${alpha})`;
            this.ctx.fillRect(0, 0, this.width, this.height);
        }

        renderOscilloscope() {
            this.clear(.15);
            const waveData = this.generateWaveData(256);
            const centerY = this.height / 2;
            const step = this.width / waveData.length;
            this.ctx.shadowBlur = 10 + this.hit * 20;
            this.ctx.shadowColor = `hsl(${(this.beat * 60) % 360}, 100%, 50%)`;
            this.ctx.beginPath();
            this.ctx.moveTo(0, centerY + waveData[0] * 200);
            for (let i = 1; i < waveData.length; i++) this.ctx.lineTo(i * step, centerY + waveData[i] * 200);
            this.ctx.strokeStyle = this.ctx.shadowColor;
            this.ctx.lineWidth = 2 + this.hit * 3;
            this.ctx.stroke();
            this.ctx.shadowBlur = 0;
        }

        renderSpectrumBars() {
            this.clear(0.2);
            const spectrum = this.generateSpectrumData(Math.floor(this.width / 15));
            const barWidth = this.width / spectrum.length;
            for (let i = 0; i < spectrum.length; i++) {
                const barHeight = spectrum[i] * this.height * 0.8;
                const x = i * barWidth, y = this.height - barHeight;
                const hue = (i * 10 + this.beat * 90) % 360;
                this.ctx.fillStyle = `hsl(${hue}, 100%, 50%)`;
                this.ctx.fillRect(x + 2, y, barWidth - 4, barHeight);
                this.ctx.fillStyle = `hsla(${hue}, 100%, 80%, ${this.hit})`;
                this.ctx.fillRect(x + 2, y - 10 - this.hit * 20, barWidth - 4, 5);
            }
        }

        renderFlowingWaves() {
            this.clear(.15);
            for (let w = 0; w < 5; w++) {
                const hue = (w * 60 + this.beat * 30) % 360;
                this.ctx.strokeStyle = `hsla(${hue}, 100%, 60%, 0.8)`;
                this.ctx.lineWidth = 2 + w + this.hit * 3;
                this.ctx.shadowBlur = this.hit * 15;
                this.ctx.shadowColor = `hsl(${hue}, 100%, 60%)`;
                this.ctx.beginPath();
                for (let x = 0; x < this.width; x += 10) {
                    const y = this.height / 2 + Math.sin(x * 0.01 + this.beat * 2 + w) * (50 + w * 20 + this.hit * 80);
                    x === 0 ? this.ctx.moveTo(x, y) : this.ctx.lineTo(x, y);
                }
                this.ctx.stroke();
            }
            this.ctx.shadowBlur = 0;
        }

        renderParticleStorm() {
            if (!this.patternData.particles) {
                this.patternData.particles = Array.from({length: 200}, () => ({
                    x: Math.random() * this.width, y: Math.random() * this.height,
                    vx: (Math.random() - 0.5) * 5, vy: (Math.random() - 0.5) * 5,
                    hue: Math.random() * 360, size: Math.random() * 3 + 1
                }));
            }
            this.clear(0.2);
            const speedMult = 1 + this.hit * 5;
            this.patternData.particles.forEach(p => {
                p.x += p.vx * speedMult; p.y += p.vy * speedMult; 
                p.hue = (p.hue + speedMult) % 360;
                if (p.x < 0) p.x = this.width; if (p.x > this.width) p.x = 0;
                if (p.y < 0) p.y = this.height; if (p.y > this.height) p.y = 0;
                this.ctx.fillStyle = `hsl(${p.hue}, 100%, ${50 + this.hit * 50}%)`;
                this.ctx.beginPath();
                this.ctx.arc(p.x, p.y, p.size + this.hit * 3, 0, Math.PI * 2);
                this.ctx.fill();
            });
        }

        renderHousePulseRings() {
            this.clear(0.25);
            const centerX = this.width / 2;
            const centerY = this.height / 2;
            
            // Sincronizzato con il beat invece che col frame
            const beatPulse = Math.abs(Math.sin(this.beat * Math.PI)); 
            const maxRadius = Math.min(this.width, this.height) * 0.45;

            for (let i = 0; i < 6; i++) {
                // Il raggio reagisce all'audio
                const radius = maxRadius * (i / 6) * beatPulse + 20 + (this.hit * 50);
                const hue = (this.beat * 60 + i * 60) % 360;

                this.ctx.strokeStyle = `hsla(${hue}, 100%, 60%, ${1 - i / 6})`;
                this.ctx.lineWidth = (8 - i) + (this.hit * 5);
                this.ctx.shadowBlur = 25 * this.hit;
                this.ctx.shadowColor = `hsl(${hue}, 100%, 60%)`;

                this.ctx.beginPath();
                this.ctx.arc(centerX, centerY, radius, 0, Math.PI * 2);
                this.ctx.stroke();
            }
            this.ctx.shadowBlur = 0;
        }

        renderSpiralWaves() {
            this.clear(0.2);
            const cx = this.width / 2, cy = this.height / 2;
            for (let s = 0; s < 6; s++) {
                const hue = (s * 60 + this.beat * 60) % 360;
                this.ctx.strokeStyle = `hsla(${hue}, 100%, 60%, 0.8)`;
                this.ctx.lineWidth = 2 + this.hit * 4;
                this.ctx.beginPath();
                for (let i = 0; i < 360; i += 5) {
                    const angle = i * Math.PI / 180 + s * Math.PI / 3 + this.beat;
                    const radius = 20 + i * 0.8 + Math.sin(i * 0.1 - this.beat * 2) * (30 + this.hit * 50);
                    const x = cx + Math.cos(angle) * radius, y = cy + Math.sin(angle) * radius;
                    i === 0 ? this.ctx.moveTo(x, y) : this.ctx.lineTo(x, y);
                }
                this.ctx.stroke();
            }
        }

        renderKaleidoscope() {
            this.clear(0.2);
            this.ctx.save();
            this.ctx.translate(this.width / 2, this.height / 2);
            this.ctx.rotate(this.beat * 0.2);
            for (let seg = 0; seg < 8; seg++) {
                this.ctx.save();
                this.ctx.rotate((seg * 2 * Math.PI) / 8);
                for (let i = 0; i < 5; i++) {
                    const angle = this.beat * (i % 2 ? 1 : -1), radius = 80 + i * 50 + this.hit * 40;
                    const size = 10 + i * 5 + this.hit * 20, hue = (i * 45 + this.beat * 90) % 360;
                    this.ctx.fillStyle = `hsla(${hue}, 100%, 60%, 0.8)`;
                    this.ctx.beginPath();
                    this.ctx.arc(Math.cos(angle) * radius, Math.sin(angle) * radius, size, 0, Math.PI * 2);
                    this.ctx.fill();
                }
                this.ctx.restore();
            }
            this.ctx.restore();
        }

        renderMatrixRain() {
            if (!this.patternData.drops) {
                this.patternData.drops = Array.from({length: 80}, () => ({
                    x: Math.random() * this.width, y: Math.random() * this.height,
                    speed: Math.random() * 5 + 2, length: Math.random() * 80 + 40
                }));
            }
            this.clear(0.2);
            const hueBase = (this.beat * 30) % 360;
            this.patternData.drops.forEach(drop => {
                drop.y += drop.speed * (1 + this.hit * 4);
                if (drop.y > this.height + drop.length) { drop.y = -drop.length; drop.x = Math.random() * this.width; }
                const gradient = this.ctx.createLinearGradient(0, drop.y - drop.length, 0, drop.y);
                gradient.addColorStop(0, `hsla(${hueBase}, 100%, 50%, 0)`);
                gradient.addColorStop(1, `hsla(${hueBase}, 100%, 50%, ${0.5 + this.hit * 0.5})`);
                this.ctx.fillStyle = gradient;
                this.ctx.fillRect(drop.x, drop.y - drop.length, 4 + this.hit * 2, drop.length);
                this.ctx.fillStyle = '#fff';
                this.ctx.fillRect(drop.x, drop.y - 5, 4 + this.hit * 2, 5 + this.hit * 10);
            });
        }

        renderCircularPulse() {
            this.clear(0.15);
            for (let i = 0; i < 6; i++) {
                const radius = ((i * 60 + this.beat * 150) % (this.width/1.5)) + this.hit * 30;
                const alpha = 1 - (radius / (this.width/1.5));
                this.ctx.strokeStyle = `hsla(${(i * 30 + this.beat * 90) % 360}, 100%, 60%, ${alpha})`;
                this.ctx.lineWidth = 4 + this.hit * 10;
                this.ctx.beginPath();
                this.ctx.arc(this.width / 2, this.height / 2, radius, 0, Math.PI * 2);
                this.ctx.stroke();
            }
        }

        renderLaserBeams() {
            this.clear(0.2);
            const cx = this.width / 2, cy = this.height / 2;
            for (let i = 0; i < 16; i++) {
                const angle = (i / 16 * Math.PI * 2) + this.beat * 0.5 * (i % 2 ? 1 : -1);
                const length = 100 + Math.sin(this.beat * 2 + i) * 50 + this.hit * this.width;
                const endX = cx + Math.cos(angle) * length, endY = cy + Math.sin(angle) * length;
                const hue = (i * 22 + this.beat * 120) % 360;
                const gradient = this.ctx.createLinearGradient(cx, cy, endX, endY);
                gradient.addColorStop(0, `hsla(${hue}, 100%, 60%, 1)`);
                gradient.addColorStop(1, `hsla(${hue}, 100%, 60%, 0)`);
                this.ctx.strokeStyle = gradient;
                this.ctx.lineWidth = 3 + this.hit * 8;
                this.ctx.beginPath();
                this.ctx.moveTo(cx, cy); this.ctx.lineTo(endX, endY);
                this.ctx.stroke();
            }
        }

        renderConfetti() {
            if (!this.patternData.confetti) {
                this.patternData.confetti = Array.from({length: 200}, () => ({
                    x: Math.random() * this.width, y: Math.random() * this.height,
                    s: Math.random() * 5 + 2, r: Math.random() * 360,
                    hue: Math.random() * 360
                }));
            }
            this.clear(0.2);
            this.patternData.confetti.forEach(p => {
                p.y += p.s * (1 + this.hit * 3); p.r += p.s * (1 + this.hit * 2);
                if (p.y > this.height) { p.y = -20; p.x = Math.random() * this.width; }
                this.ctx.save();
                this.ctx.translate(p.x, p.y);
                this.ctx.rotate(p.r * Math.PI / 180);
                this.ctx.fillStyle = `hsl(${(p.hue + this.beat * 30) % 360}, 100%, ${50 + this.hit * 50}%)`;
                this.ctx.fillRect(-5 - this.hit * 5, -10 - this.hit * 10, 10 + this.hit * 10, 20 + this.hit * 20);
                this.ctx.restore();
            });
        }

        renderFractalPulse() {
            this.clear(0.15);
            const bounce = Math.abs(Math.sin(this.beat * Math.PI)); 
            this.ctx.save();
            this.ctx.translate(this.width/2, this.height/2); 
            this.ctx.rotate(this.beat * 0.2);
            const draw = (len, d) => {
                if(!d) return;
                this.ctx.beginPath(); this.ctx.moveTo(0,0); this.ctx.lineTo(0, -len);
                this.ctx.strokeStyle = `hsla(${(this.beat*90 + d*25)%360}, 100%, 60%, ${d/6})`;
                this.ctx.lineWidth = d + this.hit * 3; this.ctx.stroke();
                this.ctx.translate(0, -len);
                const a = 0.4 + bounce * 0.6;
                this.ctx.save(); this.ctx.rotate(a); draw(len * 0.7, d - 1); this.ctx.restore();
                this.ctx.save(); this.ctx.rotate(-a); draw(len * 0.7, d - 1); this.ctx.restore();
            };
            for(let i=0; i<4; i++) {
                this.ctx.rotate(Math.PI/2);
                this.ctx.save(); draw(Math.min(this.width, this.height)*0.1 + this.hit*50, 6); this.ctx.restore();
            }
            this.ctx.restore();
        }

        renderRadialBass() {
            this.clear(0.2);
            const data = this.generateSpectrumData(64), cx = this.width / 2, cy = this.height / 2;
            const radius = 50 + this.hit * 100;
            this.ctx.beginPath();
            for(let i = 0; i < data.length; i++) {
                const angle = (i / data.length) * Math.PI * 2 + this.beat, r = radius + data[i] * 300;
                const x = cx + Math.cos(angle) * r, y = cy + Math.sin(angle) * r;
                i === 0 ? this.ctx.moveTo(x, y) : this.ctx.lineTo(x, y);
            }
            this.ctx.closePath();
            this.ctx.lineWidth = 4 + this.hit * 8; 
            const hue = (this.beat * 120) % 360;
            this.ctx.strokeStyle = `hsl(${hue}, 100%, 50%)`;
            this.ctx.fillStyle = `hsla(${hue}, 100%, 50%, 0.1)`;
            this.ctx.fill();
            this.ctx.stroke();
        }

        renderTunnel() {
            this.clear(0.2);
            const cx = this.width / 2, cy = this.height / 2;
            const maxDim = Math.max(this.width, this.height);
            for(let i = 0; i < 15; i++) {
                const size = ((this.beat * 200) + i * (maxDim / 15)) % maxDim;
                const alpha = 1 - Math.pow(size / maxDim, 2);
                this.ctx.strokeStyle = `hsla(${(i * 20 + this.beat * 60) % 360}, 100%, 50%, ${alpha})`;
                this.ctx.lineWidth = 2 + (size / maxDim) * 10 + this.hit * 10;
                this.ctx.save();
                this.ctx.translate(cx, cy);
                this.ctx.rotate(this.beat * (i % 2 ? 0.1 : -0.1));
                this.ctx.strokeRect(-size/2, -size/2, size, size);
                this.ctx.restore();
            }
        }

        switchPattern() {
            if (this.debugMode) {
                document.getElementById("debug-name").innerText = this.patterns[this.debugIndex].name;
                this.currentPattern = this.patterns[this.debugIndex].fun;
                this.debugIndex = (this.debugIndex+1)%this.patterns.length;
            } else {
                if(!this.audioInit) document.getElementById("debug-name").innerText = 'Click for Mic';
                else document.getElementById("debug-name").innerText = '';
                
                this.currentPattern = this.patterns[Math.floor(Math.random() * this.patterns.length)].fun;
            }
            this.patternData = {};
            this.startTime = Date.now();
        }

        animate() {
            const now = Date.now();
            if (this.audioInit && this.analyser) {
                this.analyser.getByteFrequencyData(this.data);
                
                const bass = (this.data[0] + this.data[1] + this.data[2] + this.data[3]) / 4; 
                
                if (bass > 150 && now - this.lastBeatT > 300) { 
                    this.lastBeatT = now;
                    this.hit = 1;
                    this.beat = Math.floor(this.beat) + 1;
                }
                
                this.hit *= 0.85; 
                this.beat += ((now - (this.lastTime || now)) / 1000) * 2; 
            } else {
                this.beat = now / 500;
                this.hit = Math.pow(1 - (this.beat % 1), 4);
            }
            
            this.lastTime = now;
            this.currentPattern();
            
            if (now - this.startTime >= (this.debugMode ? this.debugInterval : this.switchInterval)) this.switchPattern();
            requestAnimationFrame(() => this.animate());
        }

        enableDebugMode() {
            this.debugMode = true; this.debugIndex = 0; this.switchPattern();
        }

        disableDebugMode() {
            this.debugMode = false; this.debugIndex = 0; this.switchPattern();
        }
    }
    
    const visualiser = new Visualiser('visualiser');
    visualiser.animate();
    window.visualiser = visualiser;
    
    document.addEventListener('keydown', (e) => {
        if (e.key === 'd' || e.key === 'D') visualiser.debugMode ? visualiser.disableDebugMode() : visualiser.enableDebugMode();
        else if (e.key === 'n' || e.key === 'N') visualiser.switchPattern();
    });
  </script>
</body>
</html>









## 🚀 Features & Upgrades (V2)

- 🎤 **Audio-Reactive Engine**: Syncs visuals to environmental audio via device microphone using `AnalyserNode` FFT data.
- 🎨 **22 Unique Patterns**: From Oscilloscopes and Flowing Waves to Quantum Nodes, Liquid Blobs, and Neon DNA.
- 🖥️ **Ultrawide & Multi-Screen Support**: Optimized rendering radiuses and dynamic side-fillers to eliminate dead zones on wide projection setups.
- ⏱️ **Fallback BPM System**: Manual BPM adjustment mode with an on-screen HUD for scenarios where microphone access is restricted.
- 🔄 **Auto-Rotation**: Switches to a new random pattern every 3 minutes to keep the visual flow fresh.
- ⚡ **Zero Dependencies**: Pure Vanilla JS, relying entirely on native Web APIs for maximum performance and zero build-step overhead.

---

## 📸 Preview


<table align="center" style="border: none;">
  <tr>
    <td align="center" width="50%" valign="top">
      <img src="gif/video1.gif" width="100%" style="max-width: 400px; border-radius: 8px;"><br><br>
      <b>Quantum Nodes & Liquid Bob</b><br>
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

---

## 🧠 Technical Architecture

The core rendering engine is built around a highly optimized `requestAnimationFrame` loop. 
Instead of relying on heavy physics libraries, the visualizer uses native trigonometric functions (Sine/Cosine for polar coordinates) and an exponential decay logic (`hit *= 0.85`) to simulate smooth easing animations following raw audio impulses. 

Low frequencies (bass) are isolated from the FFT array to trigger beat events, ensuring the visuals pulse strictly to the kick drum of the music.

### ⌨️ Controls
- **Click anywhere**: Initialize AudioContext and request microphone permissions.
- **`N`**: Skip to the next random pattern.
- **`+/-`**: Manually increase/decrease BPM if the mic fails.
---

## ⚙️ Installation & Usage

No `npm install` or build process required.

1. Clone the repository:
   ```bash
   git clone https://github.com/mirconegri/CarnivalVisualEffects.git
   cd Carnival-Visual-Effects

 * Open pattern-drawer.html in any modern web browser.
 * Grant microphone permissions when prompted to enable audio-reactivity.
⚠️ Security Note: Most modern browsers block microphone access on file:// protocols. For local testing, serve the directory via a local web server (e.g., python3 -m http.server 8000 or VSCode Live Server).


---

## 📜 License

MIT License © 2026 `Mirco Negri` — see [LICENSE](LICENSE) file for details.

---

## 👤 Author

`Mirco Negri` GitHub: https://github.com/mirconegri
