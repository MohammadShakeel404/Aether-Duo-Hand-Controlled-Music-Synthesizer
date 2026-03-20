# 🎵 Aether Duo — Hand-Controlled Music Synthesizer

<div align="center">

![Aether Duo Banner](https://img.shields.io/badge/AETHER-DUO-8B5CF6?style=for-the-badge&labelColor=0F0A1E&color=8B5CF6)

**Two hands. One instrument. Infinite music.**

[![MediaPipe](https://img.shields.io/badge/MediaPipe-Hands%20v0.4-00BCD4?style=flat-square&logo=google)](https://mediapipe.dev)
[![Web Audio API](https://img.shields.io/badge/Web%20Audio-API-EC4899?style=flat-square)](https://developer.mozilla.org/en-US/docs/Web/API/Web_Audio_API)
[![Vanilla JS](https://img.shields.io/badge/Vanilla-JavaScript-F59E0B?style=flat-square&logo=javascript&logoColor=white)](https://developer.mozilla.org/en-US/docs/Web/JavaScript)
[![No Install](https://img.shields.io/badge/No%20Install-Required-10B981?style=flat-square)](/)
[![License](https://img.shields.io/badge/License-MIT-6B7280?style=flat-square)](LICENSE)

</div>

---

## 👤 Owner

**Mohammad Shakeel**

---

## 📖 What Is This?

Aether Duo is a browser-based musical instrument that uses your **webcam and AI hand tracking** to let you play melody and drums simultaneously — with no physical instrument, no MIDI controller, and nothing to install.

- 🤚 **Left hand** controls a full melody synthesizer
- ✋ **Right hand** triggers drum sounds via pinch gestures
- 🎵 Together you can compose, improvise, and perform live music just by moving your hands in front of your camera

Everything runs entirely in the browser using the **Web Audio API** for real-time sound synthesis and **Google's MediaPipe Hands** model for tracking 21 hand landmarks per hand at up to 30 frames per second.

---

## ✨ Features

- 🖐 **Two-hand detection** — independent melody and drum control simultaneously
- 🎹 **4 musical scales** — Pentatonic, Major, Minor, Blues
- 🔊 **4 oscillator types** — Sine, Sawtooth, Square, Triangle
- 🥁 **4 drum sounds** — Kick, Snare, Hi-Hat, Cymbal (all synthesized from scratch)
- 🎛 **Reverb + delay** — built-in echo and room reverb on the melody
- 🌊 **Live waveform** — real-time oscilloscope at the bottom of the screen
- 🎯 **Pinch-based drums** — scale-invariant gesture detection, no accidental triggers
- 💻 **You see yourself** — full camera feed visible behind the hand skeleton
- 📱 **Single file** — the entire app is one self-contained HTML file

---

## 🚀 Quick Start

> ⚠️ **Camera access requires HTTPS or localhost.** You cannot double-click the HTML file directly — the browser will block camera access.

### Option 1 — npx serve (Recommended)

Make sure you have [Node.js](https://nodejs.org) installed, then:

```bash
npx serve .
```

Open your browser and go to:

```
http://localhost:3000/aether-duo-v2.html
```

### Option 2 — Python HTTP Server

```bash
python -m http.server 8080
```

Then open:

```
http://localhost:8080/aether-duo-v2.html
```

### Option 3 — VS Code Live Server

Install the [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) extension, right-click the HTML file, and choose **Open with Live Server**.

---

> **First load note:** The MediaPipe model (~8MB) downloads on first use. This takes about 10 seconds. After that it is cached and loads instantly.

---

## 🎮 Controls

### 🤚 Left Hand — Melody

| Gesture | Action | Detail |
|---|---|---|
| Hand **up / down** | Changes pitch | Maps palm height to selected scale — 16 notes across full range |
| Hand **left / right** | Controls volume | Center = loudest, edges fade to silence |
| **Close fist** | Chord burst | Fires a 4-note chord (root, maj3rd, 5th, octave) with reverb tail |
| **Fingers extended** | Harmonics | 3 fingers = ×1.5 pitch, 4 fingers = ×2 (octave), 5 fingers = ×2.5 |

### ✋ Right Hand — Drums

| Gesture | Drum | Sound |
|---|---|---|
| **Index + thumb** pinch | 🔴 Kick | Deep bass thump, frequency sweep 180Hz → 38Hz |
| **Middle + thumb** pinch | 🟣 Snare | Tone burst + filtered white noise crack |
| **Ring + thumb** pinch | 🔵 Hi-Hat | Short high-pass noise, 8kHz cutoff, 50ms decay |
| **Pinky + thumb** pinch | 🟡 Cymbal | Long bandpass noise wash, 4.5kHz, 600ms decay |
| Fast **wrist drop** | 🟡 Cymbal | Alternative trigger — quick downward strike |

---

## 🎼 Scales

| Scale | Range | Character |
|---|---|---|
| **Pentatonic** | A2 – A5 | Always sounds musical, great for beginners and improvisation |
| **Major** | C3 – E5 | Bright, happy feel — classic Western major scale |
| **Minor** | A2 – A4 | Darker, more emotional — natural minor scale |
| **Blues** | A2 – D5 | Soulful and expressive — blues scale with b3 and b5 |

---

## 🔊 Oscillator Types

| Type | Character |
|---|---|
| **Sine** | Pure, smooth, warm tone — best for melodic playing |
| **Sawtooth** | Buzzy, bright, rich in harmonics — classic synth sound |
| **Square** | Hollow, retro — think 8-bit games or clarinets |
| **Triangle** | Softer than square, rounder than sine — flute-like |

---

## ⚙️ How It Works

### Hand Detection

The app uses Google's MediaPipe Hands model to track **21 3D landmarks** on each hand every frame. After accounting for the webcam mirror flip, each hand is routed to its instrument role — left to melody, right to drums.

### Melody Synthesis

```
Oscillator → LFO Tremolo → Gain → Analyser → Speaker
                                ↓
                         Reverb Convolver → Reverb Gain → Speaker
                                ↓
                         Delay Node (340ms) → Feedback → Speaker
```

The Y position of your palm maps to a note in the selected scale. X position controls volume. Finger count adds harmonic multipliers on top.

### Drum Detection

The drum system uses **pinch gesture recognition** — not velocity or screen zones:

1. For each finger, calculate the distance to the thumb tip in normalized landmark space
2. Divide by wrist-to-middle-finger distance for a **scale-invariant ratio** (works at any distance from camera)
3. If ratio drops below `0.07` — the finger is considered pinched
4. Drum fires only on **onset** (false → true transition), not while held
5. Each drum has a **220ms cooldown** to prevent double-triggers

This means you can hold fingers close without repeated firing, and accidental brushes won't trigger unless you deliberately close past the threshold.

---

## 🗂 File Structure

```
aether-duo-v2.html
    ├── <style>         All CSS, fonts, layout, animations
    ├── <video>         Hidden webcam feed (MediaPipe input)
    ├── <canvas>        Drum zone overlay  (drumZoneCanvas)
    ├── <canvas>        Hand skeleton      (handCanvas)
    ├── <canvas>        Waveform display   (waveCanvas)
    └── <script>        Audio engine, gesture logic, MediaPipe
```

**External CDN dependencies** (loaded on first use, then cached):

```
cdn.jsdelivr.net  →  @mediapipe/hands@0.4.1646424915
cdn.jsdelivr.net  →  @mediapipe/camera_utils@0.3.1640029074
cdn.jsdelivr.net  →  @mediapipe/drawing_utils@0.3.1620248257
fonts.googleapis.com  →  Orbitron + Syne fonts
```

---

## 🔧 Customisation

These constants near the top of the `<script>` block are the main things to tweak:

| Constant | Default | Effect |
|---|---|---|
| `PINCH_THRESHOLD` | `0.07` | Lower = harder pinch needed. Higher = more sensitive. |
| `PINCH_COOLDOWN` | `220ms` | Minimum time between two hits on the same drum. |
| `STRIKE_THRESHOLD` | `0.055` | Sensitivity of the wrist-drop cymbal trigger. |
| `delayTime` | `0.34s` | Echo delay time for the melody synth. |
| `modelComplexity` | `1` | Set to `0` for faster tracking on slower machines. |
| `maxNumHands` | `2` | Set to `1` if only using melody or drums alone. |

---

## 🛠 Troubleshooting

| Problem | Fix |
|---|---|
| **Camera blocked** | Browser Settings → Privacy → Camera → remove site from blocked list and refresh |
| **Camera busy** | Close other apps using the camera (Zoom, Teams, OBS) and retry |
| **No camera found** | Check Device Manager (Windows) or System Preferences (Mac) |
| **Model won't load** | Check internet connection — MediaPipe downloads from jsDelivr CDN on first run |
| **Drums too sensitive** | Lower `PINCH_THRESHOLD` from `0.07` to `0.05` in the script |
| **Drums not triggering** | Make sure fingers are fully closing to touch the thumb |
| **High CPU usage** | Change `modelComplexity: 1` to `modelComplexity: 0` in `hands.setOptions` |
| **Hands detected wrong side** | Move hands apart and slowly reintroduce — MediaPipe re-classifies on each frame |

---

## 🌐 Browser Support

| Browser | Support |
|---|---|
| ✅ Chrome 90+ | Full support |
| ✅ Edge 90+ | Full support |
| ⚠️ Firefox | Limited — Web Audio API differences may affect sound |
| ❌ Safari | Not supported — MediaPipe compatibility issues |

**Requires:** HTTPS or `localhost` for camera access.

---

## 📦 Tech Stack

- **[MediaPipe Hands](https://google.github.io/mediapipe/solutions/hands)** — AI hand landmark detection
- **[Web Audio API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Audio_API)** — Real-time sound synthesis
- **[Orbitron](https://fonts.google.com/specimen/Orbitron) + [Syne](https://fonts.google.com/specimen/Syne)** — Typography
- **Vanilla JavaScript** — No frameworks, no build tools

---

## 📄 License

MIT License — free to use, modify, and distribute.

---

<div align="center">

Made with ❤️ by **Mohammad Shakeel**

*Powered by MediaPipe Hands · Web Audio API · Vanilla JavaScript*

</div>
