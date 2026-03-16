# CoreXY 3D Printer — Engineering Thesis Project

![Klipper](https://img.shields.io/badge/Firmware-Klipper-blue?logo=github)
![Python](https://img.shields.io/badge/Python-3.9+-yellow?logo=python)
![Status](https://img.shields.io/badge/Status-In%20Progress%20%E2%80%94%20Hardware%20Revision-orange)
![License](https://img.shields.io/badge/License-GPL--3.0-blue)


> Engineering Bachelor's Thesis — Jakub Wolanin, 2026  
> Institute of Technology, Automation & Robotics — Industrial Automation  


## 📖 Overview

This repository contains the complete engineering documentation and source files for a custom-built high-speed CoreXY 3D printer designed from scratch. The project covers the full engineering process — from mechanical design verified by FEM simulation, through electrical system integration, to advanced firmware configuration and AI-assisted print monitoring.

The printer is built on open-source solutions (Klipper, Mainsail, Moonraker)

<img width="652" height="1156" alt="Assembled printer" src="https://github.com/user-attachments/assets/5e843b05-2f8d-431a-89c0-8483bf8fd92a" />


## ✨ Key Features

| Feature | Details |
|---------|---------|
| 🏗️ Kinematics | CoreXY with 4-motor AWD (All-Wheel Drive) — doubled torque & belt stability |
| 📐 Build Volume | 300 × 265 × 270 mm (X × Y × Z) *(see known issues — belt path limits full 310×310 bed usage)* |
| ⚙️ Frame | 2020 aluminum V-slot profiles — FEM-verified (FreeCAD CalculiX) |
| 🔧 Linear Motion | MGN15 linear rails on all axes |
| 🎯 Auto Leveling | Z-Tilt with KlickyNG magnetic probe — 0.0075 mm accuracy |
| 📳 Resonance Comp. | Input Shaper (ADXL345 accelerometer) — eliminates ringing artifacts |
| 🤖 AI Monitoring |Octoeverywhere — real-time failure detection via camera |
| 📡 Remote Access | Mainsail web UI + ZeroTier Mesh VPN — worldwide monitoring |
| 🔔 Notification* | Discord alerts on print failures |
| 💻 Firmware | [Klipper](https://www.klipper3d.org/) on Raspberry Pi 4 + BTT Octopus Pro |




## 🔬 Mechanical Design Highlights

### Frame — FEM Analysis (FreeCAD + CalculiX)
The frame (530×530×620 mm, 2020 Al profiles) was verified with modal FEM analysis:

| Profile | Resonance freq. Y-axis | Resonance freq. X-axis |
|---------|----------------------|----------------------|
| 2020 Al | 33.4 Hz | 35.6 Hz |
<img width="650" height="547" alt="FEM modal analysis of the frame X axis" src="https://github.com/user-attachments/assets/cac468ce-4b27-48fd-93b4-1df1287952a5" />
<img width="650" height="561" alt="FEM modal analysis of the frame Y axis" src="https://github.com/user-attachments/assets/e95d61bc-1817-4fdc-a846-e8ece135bc1f" />

**verification via input shaper**
<img width="885" height="665" alt="input shaper results X axis" src="https://github.com/user-attachments/assets/69eebc5f-a6b9-4977-9aa1-4adf7bdc90d5" />
<img width="886" height="664" alt="input shaper results Y axis" src="https://github.com/user-attachments/assets/c7900a8a-a875-43ec-9206-cd17b323c756" />



 Belt System
- GT2 9mm wide belts 
- Fiberglass-reinforced core 
- Non-crossed belt layout — reduced mechanical wear
- Optimal tension range: 45–55 Hz (calculated from string vibration model)

### Printhead: Dragon Burner V8
- Mounted on MGN15 rail ( too heavy, MGN9H should have been used)
- Phaetus Dragon HF hotend (High Flow)
- Sherpa Mini direct drive extruder
- Dual-fan part cooling optimized for high flow rates
- KlickyNG used as probe
---



## ⚠️ Known Issues — Hardware Revision in Progress

The current prototype revealed two mechanical design flaws that are actively being addressed:

### 1. Incorrect Motor Mount & Front-Right Idler Misalignment
- **Issue:** The front-right motor bracket is too weak causing ringing of the belt idler roller. This introduces asymmetric belt tension between the two CoreXY loops.
- **Effect:** Increased ringing artifacts on one print axis that cannot be fully compensated by Input Shaper alone.
- **Status:** 🔧 Redesigning the motor mount bracket — corrected version being modeled in FreeCAD.

### 2. Gantry Mass — Speed Limitation

<img width="1620" height="714" alt="Gantry" src="https://github.com/user-attachments/assets/230ce090-1461-46ce-a97f-bd2bc8e2abbe" />

- **Issue:** The current gantry (Dragon Burner V8 toolhead + mounting hardware + dual belts) exceeds the target mass budget, limiting achievable accelerations without introducing resonance beyond the Input Shaper's compensation range.
- **Effect:** Maximum practical print speed is below the original design target.
- **Status:** 🔧 Investigating lighter mounting hardware( carbon fiber square tube + MGN9H linear rail) and alternative toolhead configurations to reduce moving mass.

### 3. XY Axis Design — Reduced Effective Build Volume
- **Issue:** The current XY belt routing geometry places the belt path in a way that physically interferes with the printhead's range of motion on one side. The physical bed is 310×310 mm, but the usable print area is limited to **300×265 mm** because the belt blocks the toolhead from reaching the full bed surface.
- **Effect:** ~15% of the heated bed area is inaccessible — wasted heating energy and reduced print area.
- **Status:** 🔧 XY belt routing needs to be redesigned with additional clearance; corrected idler positions being evaluated.

### 4. Heated Bed — Insufficient Power & Slow Heat-Up
- **Issue:** The installed heated bed has insufficient wattage for the 310×310 mm surface area, resulting in very slow heat-up times and difficulty maintaining uniform temperature across the bed, especially at corners.
- **Effect:** Long wait times before printing; possible temperature gradients causing adhesion issues near bed edges.
- **Status:** 🔧 Evaluating higher-wattage bed heater or AC mains bed (via SSR relay) as a replacement.

### 5. Bed Z-Stability — 3-Point Mount Sagging (~0.2 mm First Layer Variation)

<img width="1834" height="813" alt="heightmap of the heated bed" src="https://github.com/user-attachments/assets/b5730210-b2f1-479c-97bc-05aefc7fe9d8" />

- **Issue:** The heated bed is attached to the Z-axis using a 3-point mounting system. To adapt from the bed's 2-hole pattern to the 3-point lead screw layout, a bridge/adapter beam was used. This introduces structural flex — the bed sags between support points, causing a measurable height variation of approximately **0.2 mm** across the first layer, visible in bed mesh scans.
- **Effect:** First layer consistency is compromised; mesh compensation partially corrects this, but the flex remains a source of instability.
- **Status:** 🔧 A 4-point mounting design (direct attachment without adapter beam) is being developed to eliminate the flex and improve Z-plane rigidity.

> 📸 *Bed mesh scan photo documenting the Z variation will be added to the `assets/` directory.*

---

## 📁 Repository Structure



## 🚀 Quick Start — Klipper Config

All configuration files are in [`klipper_config/`](./klipper_config/).


> ⚠️ **Important:** Pin assignments in `printer.cfg` are specific to the **BTT Octopus Pro** board with the motor layout described in the thesis. Adjust per your hardware.


## 🛠️ Technologies Used

- **Firmware:** [Klipper](https://www.klipper3d.org/) + [Moonraker](https://github.com/Arksine/moonraker)
- **Printer Control (AI):** [OctoPrint](https://octoprint.org/)
- **Web UI:** [Mainsail](https://docs.mainsail.xyz/)
- **Camera:** Crowsnest stream server
- **VPN:** ZeroTier Mesh VPN
- **FEM Analysis:** FreeCAD + CalculiX solver
- **CAD:** FreeCAD
- **MCU:** BTT Octopus Pro (STM32F429)
- **Host:** Raspberry Pi 4

---

## 📜 License & Open-Source Credits

This project is released under the **[GPL-3.0 License](./LICENSE)**.

It builds upon the following open-source projects:

| Project | License | Repository |
|---------|---------|------------|
| Klipper | GPL-3.0 | [github.com/Klipper3d/klipper](https://github.com/Klipper3d/klipper) |
| Moonraker | GPL-3.0 | [github.com/Arksine/moonraker](https://github.com/Arksine/moonraker) |
| Mainsail | GPL-3.0 | [github.com/mainsail-crew/mainsail](https://github.com/mainsail-crew/mainsail) |
| Dragon Burner V8 | GPL-3.0 | [github.com/chirpy2605/voron-user-mods](https://github.com/chirpy2605/voron-user-mods) |
| Sherpa Mini Extruder | GPL-3.0 | [github.com/Annex-Engineering/Sherpa_Mini-Extruder](https://github.com/Annex-Engineering/Sherpa_Mini-Extruder) |
| KlickyNG Probe | GPL-3.0 | [github.com/jlas1/Klicky-Probe](https://github.com/jlas1/Klicky-Probe) |
| OctoPrint | AGPL-3.0 | [github.com/OctoPrint/OctoPrint](https://github.com/OctoPrint/OctoPrint) |

All original design work, configuration, documentation and code in this repository is © 2026 Jakub Wolanin and licensed under GPL-3.0.

---


## 👤 Author

**Jakub Wolanin**  
Engineering thesis — Automation & Robotics, Industrial Automation specialization  
Krosno 2026
contact:
jakubwolanin77@gmail.com
