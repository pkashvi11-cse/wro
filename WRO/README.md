<div align="center">

# 🤖 WRO 2026 – Future Engineers
# Team **BYTE RIDERS**

**World Robot Olympiad 2026 · Future Engineers Category**
**CHARUSAT — Charotar University of Science and Technology, India**

[![WRO](https://img.shields.io/badge/WRO-2026-blue)](https://wroindia.org)
[![Category](https://img.shields.io/badge/Category-Future%20Engineers-orange)]()
[![Country](https://img.shields.io/badge/Country-India-green)]()
[![Status](https://img.shields.io/badge/Status-In%20Development-yellow)]()

`<!-- TODO: Add team logo / banner image here, e.g. ./t-photos/team_logo.png -->`

</div>

---

## 📑 Table of Contents

1. [Team Introduction](#1-team-introduction)
2. [Repository Structure](#2-repository-structure)
3. [Project Overview](#3-project-overview)
4. [Mobility Management — Mechanical Design](#4-mobility-management--mechanical-design)
5. [Power & Sense Management — Electronics](#5-power--sense-management--electronics)
6. [Obstacle Management — Software & Control](#6-obstacle-management--software--control)
7. [System Architecture](#7-system-architecture)
8. [Engineering Specifications](#8-engineering-specifications)
9. [Component & Power Distribution Table](#9-component--power-distribution-table)
10. [Vehicle Photos](#10-vehicle-photos)
11. [Performance & Testing](#11-performance--testing)
12. [Video Demonstrations](#12-video-demonstrations)
13. [How to Reproduce This Robot](#13-how-to-reproduce-this-robot)
14. [Bill of Materials](#14-bill-of-materials)
15. [Challenges & Learnings](#15-challenges--learnings)
16. [Future Improvements](#16-future-improvements)
17. [Acknowledgments & References](#17-acknowledgments--references)
18. [License](#18-license)

---

## 1. Team Introduction

**Team Name:** Byte Riders
**Institution:** CHARUSAT — Charotar University of Science and Technology
**Competition:** World Robot Olympiad 2026, Future Engineers Category

| Photo | Name | Role | Responsibilities |
|---|---|---|---|
| `<!-- TODO: photo -->` | **Shrut Barasara** | Mechanical & Hardware Lead | Chassis design, 4-wheel steering geometry, sensor mounting, wiring, power distribution |
| `<!-- TODO: photo -->` | **Happy Patel** | Software & Version Control Engineer | Embedded firmware (ESP32-S3), vision pipeline (OpenCV), Git/GitHub workflow, repository management |
| `<!-- TODO: photo -->` | **`[MISSING: Coach name]`** | Coach | `[MISSING: coach responsibilities]` |

> `[MISSING]` A short paragraph introducing the team — how the team formed, what drew you to Future Engineers, and your overall approach/philosophy to the build. (2–4 sentences works well here and scores well on "team introduction" criteria in most WRO rubrics.)

`<!-- TODO: Add a team photo — see Section 10 for the full photo checklist -->`

---

## 2. Repository Structure

```
WRO-2026-FutureEngineers-BYTERIDERS-INDIA/
│
├── docs/
│   ├── components/              # One .md datasheet-reference file per component
│   │   ├── raspberry_pi_4b.md
│   │   ├── esp32_s3.md
│   │   ├── l298n.md
│   │   ├── mg995.md
│   │   ├── mpu6050.md
│   │   ├── qmc5883l.md
│   │   ├── vl53l0x.md
│   │   ├── vl53l1x.md
│   │   ├── drive_motor.md
│   │   └── lipo_battery.md
│   ├── power/
│   │   └── POWER_DISTRIBUTION.md
│   └── wiring/
│       └── WIRING.md
│
├── models/                      # CAD source files + exported STL/STEP for all printed & machined parts
│   └── `[MISSING: confirm exact folder name / contents]`
│
├── schemes/                     # Circuit + wiring schematics (PDF / PNG / Fritzing)
│   └── `[MISSING: add schematic files]`
│
├── src/
│   ├── open_challenge/          # Firmware + vision code for the Open Challenge
│   └── obstacle_challenge/      # Firmware + vision code for the Obstacle Challenge
│
├── t-photos/                    # Team photo(s)
├── v-photos/                    # Vehicle photos — front, back, left, right, top, bottom
├── video/
│   └── video.md                 # Links to Open & Obstacle Challenge demo videos
│
├── LICENSE
└── README.md                    # You are here
```

> **Note:** This structure mirrors the folder layout already referenced inside your `docs/` files (`docs/power/POWER_DISTRIBUTION.md`, `docs/wiring/WIRING.md`). Keep this section updated the moment you add or rename a folder — evaluators check that the README structure and the actual repo structure match exactly.
>
> `[MISSING]` I could not browse your live repository tree directly (GitHub blocks automated crawling), so please confirm/correct the folder names above against what actually exists in your repo before publishing.

---

## 3. Project Overview

Team Byte Riders' vehicle is a fully autonomous, self-driving robot built for the WRO 2026 Future Engineers category. The car must complete the **Open Challenge** (three laps of an unknown, randomly-configured track using only wall/edge sensing) and the **Obstacle Challenge** (three laps while dynamically detecting and avoiding red/green traffic pillars, then performing precision parallel parking) with zero human intervention after the start signal.

### Design Philosophy
The vehicle uses a **camera-based perception system** running on a Raspberry Pi 4B for high-level decision-making (pillar color/parking-block detection), paired with a dedicated **ESP32-S3 real-time controller** that closes the low-level steering/motor control loop independently of the vision pipeline's frame rate. This split-brain architecture keeps actuator response deterministic (100 Hz) even while the Pi is busy processing camera frames.

### Key Design Choices
- **4-Wheel Steering (4WS)** using Ackermann steering geometry on both axles, for a significantly tighter turning radius than front-wheel steering — important given the WRO Future Engineers arena's tight corners.
- **Sensor fusion** of a monocular camera (OpenCV, color-based pillar/parking detection), three Time-of-Flight distance sensors (front/left/right), and a 6-DoF IMU for heading correction and drift compensation.
- **Deterministic actuator control**: the Pi streams high-level commands to the ESP32-S3 over a binary, CRC8-checked serial protocol at 100 Hz, so steering/motor response is never blocked by vision processing latency.

> `[MISSING]` Add your problem statement / mission statement in your own words (WRO rubrics generally reward a clearly stated "what problem does this robot solve, and how" paragraph). A good template: *"How can an autonomous vehicle reliably [complete X] using [sensor set] while satisfying WRO Rule 11.1 size constraints?"*

---

## 4. Mobility Management — Mechanical Design

### Steering
The vehicle uses a **4-Wheel Steering (4WS) system based on Ackermann steering theory**, applied symmetrically to the front and rear axles. Unlike a standard front-wheel-steering (FWS) layout, 4WS lets the rear wheels counter-steer relative to the front, which:
- Shrinks the turning radius substantially (see [Section 8](#8-engineering-specifications) — **~44.9% smaller than an equivalent FWS layout**).
- Improves cornering stability on the tight WRO track segments.

### Drivetrain
- **Drive motor:** Johnson-type geared DC motor, **300 RPM**, driven through an **L298N** dual H-bridge driver (`ENA` → speed/PWM, `IN1`/`IN2` → direction).
- **Measured steering range:** **35°–40°** at the wheel, matching the ±35° design target in the specification table below.
- **Chassis material:** PETG with 30% gyroid infill, chosen for isotropic stiffness and heat resistance (T<sub>g</sub> ≈ 80 °C) — see justification table in [Section 8](#8-engineering-specifications).

### CAD & Mechanical Files
> `[MISSING]` Link or embed your CAD screenshots/renders here, and confirm the `models/` folder actually contains editable CAD source (Fusion 360 / SolidWorks / STEP) **and** exported STL files — rubrics specifically check for "CAD/wiring/code files included."

`<!-- TODO: Add chassis/steering assembly renders or photos here -->`

---

## 5. Power & Sense Management — Electronics

### High-Level Sensing (Raspberry Pi 4B)
- **Pi Camera v2** — OpenCV-based red/green pillar detection and magenta parking-block detection.
- **VL53L1X** Front ToF — I²C address `0x30`, `XSHUT` on GPIO 22.
- **VL53L0X** Left ToF — I²C address `0x31`, `XSHUT` on GPIO 17.
- **VL53L0X** Right ToF — I²C address `0x32`, `XSHUT` on GPIO 27.
- **MPU6050** 6-DoF IMU — I²C address `0x68`.

### Inter-Processor Link
The Raspberry Pi 4B communicates with the ESP32-S3 over **USB serial**, using a **10-byte, CRC8-checked binary packet streamed at 100 Hz**. This keeps the safety-critical steering/motor loop running on dedicated real-time hardware, decoupled from the Pi's (variable-latency) vision pipeline.

### Real-Time Actuation (ESP32-S3)
- **MG995 servo** — 4WS steering actuator, GPIO 18, 50 Hz PWM.
- **L298N `ENA`** — motor speed control, GPIO 19, PWM.
- **L298N `IN1`** — direction control, GPIO 20.
- **L298N `IN2`** — direction control, GPIO 21.

### Power Rails
See the full [Component & Power Distribution Table](#9-component--power-distribution-table) below — all rail definitions live in `docs/power/POWER_DISTRIBUTION.md`, and pin-level connections live in `docs/wiring/WIRING.md`.

`<!-- TODO: Add a labelled photo of the wiring/electronics bay -->`

> `[MISSING]` A few things that strengthen this section for evaluators:
> - Battery capacity (mAh) and estimated runtime per charge.
> - A fritzing/KiCad schematic or hand-drawn wiring diagram image (referenced from `schemes/`).
> - Fusing / reverse-polarity / brown-out protection details, if any.

---

## 6. Obstacle Management — Software & Control

### Perception
- **Color-based pillar detection (OpenCV):** HSV thresholding identifies red and green pillars in the camera frame; the robot passes red pillars on the right and green pillars on the left (per WRO Future Engineers rules).
- **Magenta parking-block detection:** a dedicated HSV mask locates the parking-lot marker for the precision-parking maneuver at the end of each run.
- **ToF wall-following:** the left/right VL53L0X sensors maintain a target offset from the inner wall; the front VL53L1X sensor triggers corner/obstacle response.
- **IMU heading correction:** the MPU6050 supplies yaw-rate data used to correct for drift between vision updates and to execute clean, repeatable turns.

### Control Loop
- **Control loop rate:** 100 Hz on the ESP32-S3 (5× the ~10 Hz mechanical bandwidth of the servo/motor — see justification in [Section 8](#8-engineering-specifications)).
- **Serial link:** 115,200 baud, <9% UART utilization at the 100 Hz packet rate — leaves comfortable headroom for retries/CRC handling.

> `[MISSING]` This is the section evaluators scrutinize most closely for the Obstacle Challenge score. Please add:
> - The actual control algorithm (state machine diagram, or PID/pure-pursuit block diagram) used for wall-following and pillar avoidance.
> - PID gains (or equivalent tuning constants) and how they were tuned.
> - How the parking maneuver is triggered and executed (open-loop timed sequence vs. closed-loop using ToF/vision feedback).
> - Any calibration routine (camera HSV calibration, ToF offset calibration, IMU zeroing) and where it lives in `src/`.
> - A short code snippet or flowchart from `src/obstacle_challenge/` illustrating the main loop.

---

## 7. System Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│  Raspberry Pi 4B  (High-Level: Perception, Navigation & Control) │
│                                                                   │
│  ├── Pi Camera v2                                                │
│  │     (OpenCV Red/Green Pillar + Magenta Parking Block Detect)  │
│  ├── VL53L1X Front ToF   (I2C 0x30, XSHUT → GPIO 22)             │
│  ├── VL53L0X Left  ToF   (I2C 0x31, XSHUT → GPIO 17)             │
│  ├── VL53L0X Right ToF   (I2C 0x32, XSHUT → GPIO 27)             │
│  └── MPU6050 6-DoF IMU   (I2C 0x68)                              │
└──────────────────────────────┬────────────────────────────────────┘
                                │  USB Serial
                                │  10-byte CRC8 Binary Packet @ 100 Hz
                                ▼
┌─────────────────────────────────────────────────────────────────┐
│         ESP32-S3  (Real-Time Motor & Steering Controller)        │
│                                                                   │
│  ├── MG995 Servo  — 4WS Steering   (GPIO 18, PWM 50 Hz)          │
│  ├── L298N ENA    — Motor Speed    (GPIO 19, PWM)                │
│  ├── L298N IN1    — Direction      (GPIO 20)                     │
│  └── L298N IN2    — Direction      (GPIO 21)                     │
└─────────────────────────────────────────────────────────────────┘
```

> `[MISSING]` Consider re-drawing this as an actual embedded image (PNG/SVG) exported from draw.io, Figma, or similar, and dropping it in `schemes/` — GitHub renders images more reliably across viewers than ASCII art, and a clean architecture diagram is a strong signal for the "Reproducibility & GitHub Quality" rubric line.

---

## 8. Engineering Specifications

| Parameter | Value | Justification |
|---|---|---|
| Vehicle Length | 230 mm | 23% margin under 300 mm WRO Rule 11.1 limit |
| Vehicle Width | 160 mm | 20% margin under 200 mm WRO Rule 11.1 limit |
| Wheelbase | 160 mm | 50:50 weight distribution, optimal pitch stability |
| Track Width | 130 mm | Rollover threshold 1.86 g ≫ max grip 0.80 g |
| Turning Radius (4WS) | ~126 mm | 44.9% smaller than FWS equivalent |
| Max Steering Angle | ±35° | CVD joint binding hard-stop limit |
| Rear/Front Steering Ratio (κ) | 0.85 | Optimal inner-wall clearance in tight corners |
| Control Loop Rate | 100 Hz | 5× Nyquist margin over 10 Hz actuator bandwidth |
| Serial Baud Rate | 115,200 | <9% UART utilization at 100 Hz packet rate |
| Chassis Material | PETG, 30% gyroid infill | Isotropic stiffness, T<sub>g</sub> 80 °C heat resistance |
| Drive Motor | Johnson-type DC, 300 RPM | `[MISSING: justification]` |
| Measured Turning Angle | 35°–40° | Bench-measured, matches ±35° design spec |

> `[MISSING]` Add rows for: total vehicle mass, ground clearance, wheel diameter/type, gear ratio (if any) between motor and wheels, and battery weight — these round out a "specs" table nicely and are easy wins for the rubric's "commit history / README structure" line since they show thorough documentation.

---

## 9. Component & Power Distribution Table

| Component | File | Power Rail | Interface | Official Datasheet |
|---|---|---|---|---|
| Raspberry Pi 4 Model B | [`raspberry_pi_4b.md`](docs/components/raspberry_pi_4b.md) | 5V rail | GPIO, I2C1, UART | [PDF](#) |
| ESP32-S3 | [`esp32_s3.md`](docs/components/esp32_s3.md) | Pi USB (5V) | UART, PWM | [PDF](#) |
| L298N driver module | [`l298n.md`](docs/components/l298n.md) | Motor rail 11.1V | ENA / IN1 / IN2 | [PDF](#) |
| MG995 steering servo | [`mg995.md`](docs/components/mg995.md) | Servo rail (UBEC 5V) | 50 Hz PWM | [PDF](#) |
| MPU6050 IMU | [`mpu6050.md`](docs/components/mpu6050.md) | Pi 3.3V | I2C | [PDF](#) |
| QMC5883L magnetometer | [`qmc5883l.md`](docs/components/qmc5883l.md) | Pi 3.3V | I2C | [PDF](#) |
| VL53L0X ToF (left/right) | [`vl53l0x.md`](docs/components/vl53l0x.md) | Pi 3.3V | I2C | [PDF](#) |
| VL53L1X ToF (front) | [`vl53l1x.md`](docs/components/vl53l1x.md) | Pi 3.3V | I2C | [PDF](#) |
| Drive motor (AWD) | [`drive_motor.md`](docs/components/drive_motor.md) | Motor rail (via L298N) | PWM DC | none — bench measured |
| LiPo 3S battery | [`lipo_battery.md`](docs/components/lipo_battery.md) | source (11.1V) | XT60 | none — manufacturer data |

*All power rails are defined in [`docs/power/POWER_DISTRIBUTION.md`](docs/power/POWER_DISTRIBUTION.md); pin connections are in [`docs/wiring/WIRING.md`](docs/wiring/WIRING.md).*

> `[MISSING]` Replace the `#` placeholder datasheet links with the real manufacturer PDF URLs — a real, working link per component is exactly what the "GitHub structure and clarity" rubric line is checking. Also worth noting: you list a **QMC5883L magnetometer** here but it isn't mentioned in the system architecture text you gave me — please confirm whether it's actually on the final robot (and if so, add it to Sections 5 & 7), or remove it if it was dropped from the design.

---

## 10. Vehicle Photos

Per WRO documentation norms, include **six vehicle photos**: front, back, left, right, top, and bottom, stored in `v-photos/`.

| Front | Back | Left |
|---|---|---|
| `<!-- TODO -->` | `<!-- TODO -->` | `<!-- TODO -->` |

| Right | Top | Bottom |
|---|---|---|
| `<!-- TODO -->` | `<!-- TODO -->` | `<!-- TODO -->` |

> `[MISSING — full photo checklist]`
> 1. Vehicle — front, back, left, right, top, bottom (6 photos, plain background, good lighting)
> 2. Team photo — all members together, ideally with the robot
> 3. Individual member photos (used in Section 1)
> 4. Close-up of the electronics/wiring bay
> 5. Close-up of the 4WS steering mechanism
> 6. Close-up of the sensor mounting (camera + ToF array)
> 7. CAD renders/screenshots of the chassis and steering assembly
> 8. Photos or scan of the wiring schematic

---

## 11. Performance & Testing

> `[MISSING]` This section is currently empty — please provide (even rough/approximate figures are fine, they can be refined later):
> - Best/average lap time for the Open Challenge (practice runs).
> - Best/average lap time for the Obstacle Challenge.
> - Success rate of pillar detection and parking across N test runs.
> - Any calibration log or test data table you'd like included (a small markdown table works well here).
> - Known failure modes and how you mitigated them (e.g., "camera detection degrades under direct overhead light — added a diffuser / adjusted HSV range").

---

## 12. Video Demonstrations

| Challenge | Link |
|---|---|
| Open Challenge | `[MISSING: YouTube/Drive link]` |
| Obstacle Challenge | `[MISSING: YouTube/Drive link]` |

Full details and links live in [`video/video.md`](video/video.md).

---

## 13. How to Reproduce This Robot

This section is what lets another team rebuild your exact robot from this repository alone — directly addresses the rubric's "Can another team reproduce this robot?" criterion.

### Mechanical
1. Print/source the chassis parts from `models/` (PETG, 30% gyroid infill recommended, see [Section 8](#8-engineering-specifications)).
2. Assemble the 4-wheel Ackermann steering linkage per the CAD assembly drawing in `models/`.
3. Mount the Johnson 300 RPM drive motor and confirm the measured steering range (35°–40°) matches spec before proceeding.

### Electronics
1. Wire all components exactly as documented in [`docs/wiring/WIRING.md`](docs/wiring/WIRING.md) and the [power table above](#9-component--power-distribution-table).
2. Double-check I²C addresses for the three ToF sensors (`0x30`/`0x31`/`0x32` — note the two VL53L0X units share the same part but different addresses, set via `XSHUT` sequencing at boot) and the MPU6050 (`0x68`).

### Software
1. Flash the ESP32-S3 with the firmware in `src/.../esp32/` `[MISSING: confirm exact path]`.
2. Set up the Raspberry Pi 4B with the dependencies in `[MISSING: requirements.txt or setup instructions — please add one]`.
3. Run the calibration routine (`[MISSING: script name]`) to tune HSV thresholds for the competition lighting.
4. Launch the main control script: `[MISSING: e.g. python3 src/open_challenge/main.py]`.

> `[MISSING]` Please fill in the bracketed items above with your actual file paths and commands — a reviewer (or another team) should be able to follow this section top-to-bottom with zero guessing. Consider adding a `requirements.txt` (Python) and/or `platformio.ini` (ESP32) at the repo root if you don't have one already — that alone is a strong, easy signal for "reproducibility."

---

## 14. Bill of Materials

> `[MISSING]` A BOM table (component, quantity, approximate cost, source/vendor) isn't explicitly listed in the rubric row you shared, but it's a common Future Engineers scoring criterion and costs little to add. Let me know if you'd like this generated from the component table in Section 9 and I'll fill it in once you send costs/vendors.

---

## 15. Challenges & Learnings

> `[MISSING]` A short, honest "what went wrong and how we fixed it" section (2–5 bullet points) is one of the highest-value additions you can make — it's what separates a documentation dump from genuine engineering journaling, and most rubrics reward it under README quality/depth. Example prompts:
> - What was the hardest part of getting 4WS geometry to behave correctly?
> - Did the Pi ↔ ESP32 serial link have any early reliability issues, and how did the CRC8 packet design solve them?
> - Any camera/lighting calibration surprises?

---

## 16. Future Improvements

> `[MISSING]` 2–4 bullet points on what you'd do differently or add with more time (e.g., closed-loop parking using vision feedback instead of a timed sequence, encoder-based odometry, etc.).

---

## 17. Acknowledgments & References

- World Robot Olympiad Association & WRO India — competition rules and guidelines.
- `[MISSING: coach name]` and CHARUSAT for mentorship and lab/workshop access.
- `[MISSING]` Any open-source libraries, tutorials, or prior-year teams' repositories that informed this design (citing prior work you learned from is good practice, not a weakness).

---

## 18. License

> `[MISSING]` Add a `LICENSE` file at the repo root and state it here (MIT is the most common choice for WRO open-source repos, e.g. Team Apollo's reference repo above uses MIT).

---

<div align="center">

**Built by Team Byte Riders 🚗⚡ — CHARUSAT**
*WRO 2026 Future Engineers*

</div>