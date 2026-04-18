# ❄️ SnowGuard — Autonomous Smart Roof Snow Clearance System

> **Chillai Kalan Hackathon** · SSM College of Engineering 

![Status](https://img.shields.io/badge/status-prototype-blue)
![Platform](https://img.shields.io/badge/platform-ESP32-green)
![Simulation](https://img.shields.io/badge/simulation-Wokwi-orange)
![License](https://img.shields.io/badge/license-MIT-lightgrey)

---

## 🏔️ The Problem

Kashmir experiences some of the most extreme snowfall in the region during **Chillai Kalan** — the 40-day harsh winter period (December 21 to January 31). Every year, rooftops across rural Kashmir silently accumulate dangerous snow loads, pushing structures toward sudden and often fatal collapse.

**Who is most at risk:**
- Low-income families with traditional masonry and tin roofs
- Elderly residents living alone
- Rural homeowners far from emergency services
- Schools and mosques with large unsupported roof spans

**Why the problem persists:**
- No affordable real-time snow load monitoring technology exists
- Manual snow removal is dangerous, physically demanding, and often delayed
- Traditional roof structures lack sufficient load tolerance
- Residents lack awareness of accumulating danger until it is too late

---

## 💡 Our Solution

**SnowGuard** is a closed-loop, sensor-driven autonomous system that monitors roof snow load in real time and triggers mechanical snow clearance before structural failure occurs — eliminating the need for risky manual removal.

### How it works

```
Snow accumulates on roof
        ↓
Load Cell measures weight every 500ms
        ↓
HX711 amplifies and digitises the signal
        ↓
ESP32 compares reading to safety threshold
        ↓
Threshold exceeded → Buzzer + Red LED + MQTT alert fires
        ↓
Servo motor rotates sweep arm → clears snow off slope
        ↓
Load drops below safe limit → Green LED ON → System resets
```

---

## 🖥️ Web Dashboard

The dashboard runs in **Simulation Mode** (for demo/presentation) and **Live Mode** (connected to physical ESP32 via MQTT/WiFi).

| State | Snow Load | Gauge Colour | Indicator |
|-------|-----------|-------------|-----------|
| ✅ SAFE | 0 – 150 kg/m² | 🟢 Green | Green LED ON |
| ⚠️ WARNING | 150 – 250 kg/m² | 🟡 Yellow | Servo primed |
| 🚨 CRITICAL | > 250 kg/m² | 🔴 Red | Sweep triggered + Alert |

**Dashboard features:**
- Real-time snow load gauge (kg/m²)
- Roof surface temperature (live)
- Load vs safe limit percentage bar
- Alerts triggered counter (session)
- Location tag: Sopore, Kashmir — Sensor Array A1
- Toggle between Simulation and Live (ESP32) mode

---

## 🔧 Hardware Components

| # | Component | Role | Qty | Price (₹) |
|---|-----------|------|-----|-----------|
| A | Load Cell (50kg) | Measures snow weight on roof | 2 | 300–400 each |
| B | HX711 Amplifier | Boosts load cell signal for ESP32 | 1 | 80–100 |
| C | MG996R Servo Motor | Sweeps snow off roof slope | 1 | 300–400 |
| D | ESP32 Dev Board | Brain — reads, decides, alerts | 1 | 400–500 |
| E | Green LED | Safe status indicator | 1 | 5 |
| F | Red LED | Overload warning indicator | 1 | 5 |
| G | Active Buzzer | Audio alarm on threshold breach | 1 | 20–30 |
| H | 12V DC Adapter + Buck Converter | Powers entire system | 1 | 360 |
| I | 330Ω Resistors | LED current protection | 2 | 5 |
| J | Jumper Wires + Breadboard | Connections | — | 180 |
| K | Weatherproof Plastic Box | Enclosure for ESP32 on wall | 1 | 150–250 |

**Estimated Total: ₹1,800 – ₹2,500**

---

## 🗺️ Component Placement

```
         ROOF RIDGE (peak)
              │
         [SERVO MOTOR]  ← hot-glued here, arm sweeps left slope
              │
    ┌─────────┴──────────┐
    │   LEFT SLOPE       │  RIGHT SLOPE
    │   [SNOW]           │  (cleared)
    │   ↓↓↓ weight       │
    │ [LOAD CELL]        │  ← sandwiched under roof panel
    │ [HX711]            │  ← taped to inner roof surface
    └────────────────────┘
              │ wires run down inside wall
    ┌──────────────────────┐
    │  [ESP32 BOX]         │  ← mounted on side wall, mid-height
    │  Green LED  🟢       │
    │  Red LED    🔴       │
    │  Buzzer     🔔       │
    └──────────────────────┘
              │ WiFi/MQTT
    ┌──────────────────────┐
    │  [POWER SUPPLY]      │  ← inside house, ground level
    │  12V → Buck → 5V     │
    └──────────────────────┘
```

---

## 📌 ESP32 Wiring Reference

| ESP32 Pin | Direction | Connected To |
|-----------|-----------|--------------|
| GPIO 34 | ← INPUT | HX711 DOUT (data) |
| GPIO 35 | ← INPUT | HX711 SCK (clock) |
| GPIO 18 | → OUTPUT | Servo PWM signal |
| GPIO 2 | → OUTPUT | Green LED (via 330Ω) |
| GPIO 4 | → OUTPUT | Red LED (via 330Ω) |
| GPIO 15 | → OUTPUT | Buzzer (+) pin |
| 3V3 | POWER | HX711 VCC |
| GND | GROUND | HX711 GND, Servo GND, LED (−), Buzzer GND |

---

## 🧪 Wokwi Simulation

The full circuit is simulated on **[Wokwi](https://wokwi.com)** — no physical hardware needed to test the logic.

**Wokwi project:** `wokwi.com/projects/461560955805580289`

### How to run the simulation

1. Open the Wokwi project link above
2. Click the **▶ Play** button
3. **Rotate the potentiometer knob** to simulate increasing snow weight
4. Watch:
   - Serial monitor prints weight in grams
   - Green LED ON when below threshold
   - At ~300g: Red LED flashes + Buzzer beeps + Servo sweeps
   - After sweep: weight drops → Green LED returns

### Libraries required (`libraries.txt`)
```
HX711@0.7.5
ESP32Servo@0.11.0
```

### Files

| File | Description |
|------|-------------|
| `sketch.ino` | Main Arduino code for ESP32 |
| `diagram.json` | Wokwi circuit diagram |
| `libraries.txt` | Required library versions |

---

## 📁 Repository Structure

```
snowguard/
├── firmware/
│   ├── sketch.ino          # ESP32 Arduino code
│   ├── diagram.json        # Wokwi circuit diagram
│   └── libraries.txt       # Wokwi library dependencies
├── dashboard/
│   └── index.html          # Web dashboard (Simulation + Live mode)
├── docs/
│   ├── component_guide.pdf # Full component explanations
│   ├── sensor_placement.pdf# Where to mount every component
│   └── 3d_model.pdf        # House prototype sketches (all views)
└── README.md
```

---

## 🚀 Running the Dashboard Locally

```bash
# Clone the repository
git clone https://github.com/YOUR_USERNAME/snowguard.git
cd snowguard

# Open dashboard directly in browser
open dashboard/index.html

# Or serve with Python
python -m http.server 8000
# then open http://localhost:8000/dashboard/
```

No build step, no dependencies — pure HTML/CSS/JS.

---

## 📡 Live Mode (Physical ESP32)

When connected to a real ESP32:

1. Flash `firmware/sketch.ino` to your ESP32 (Arduino IDE or PlatformIO)
2. Update WiFi credentials and MQTT broker IP in the sketch
3. Run a local MQTT broker (e.g. Mosquitto):
   ```bash
   mosquitto -v
   ```
4. Open the dashboard and click **Live (ESP32)** tab
5. Dashboard subscribes to MQTT topic `snowguard/load` for live readings

---

## 👥 Team

| Name | Role |
|------|------|
| Alima Qadir | Hardware + Firmware + Dashboard |
| Saqlain Bashir | Presentation + System Design |
| Zainab Amin | Research + Presentation |
| Muskan Ayoub | Testing + Component Assembly |

**Institution:** SSM College of Engineering, Parihaspora, Sopore
**Event:** Chillai Kalan Hackathon — University-level round

---

## 🌍 Why This Matters

Kashmir receives 200–400cm of snowfall annually in heavy winters. Roof collapses during Chillai Kalan are not rare events — they are a recurring crisis affecting the most vulnerable communities. SnowGuard is designed specifically for this geography, climate, and socioeconomic context.

The system is:
- **Affordable** — under ₹2,500 in components
- **Autonomous** — no human intervention needed for clearance
- **Local** — built for Kashmiri roof geometry and snowfall patterns
- **Scalable** — modular design fits different roof types and sizes

---

## 📄 License

MIT License — free to use, modify, and build upon.

---

*Built in Sopore, Kashmir · Winter 2026*
