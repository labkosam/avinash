# Automated Wall Painting Robot
**A Future-Ready Solution for Cost-Effective and Hazard-Free Construction**

Project Code: 5510 | Author: Matta Avinash | Co-Author and Guide: A. Raghu Ram, Senior Professor and Vice Principal, JNTU Hyderabad

---

## Overview

An intelligent wall painting robot that uses computer vision and embedded robotics to automate painting tasks — removing humans from hazardous environments while delivering consistent, uniform coverage.

The system:
- Captures wall images via camera (webcam / PiCamera)
- Detects wall boundaries using adaptive thresholding (OpenCV)
- **Safety check**: halts immediately if a human face is detected in frame
- Generates a vertical zigzag painting path for 100% wall coverage
- Controls NEMA 17 stepper motors at 20 steps/cm precision
- Activates a DCV spray valve synchronized at each motor coordinate
- Regulates motor speed via PWM (100 Hz)
- Models a cable-driven dual-motor system for 5×5 m workspace coverage

---

## Project Structure

```
project_paint/
├── main.py                  ← Entry point: run simulation here
│
├── simulation/              ← Modular simulation package
│   ├── gpio.py              ←   MockGPIO (simulates RPi.GPIO)
│   ├── camera.py            ←   MockPiCamera (webcam via OpenCV)
│   ├── image_processing.py  ←   Face detection + wall segmentation
│   └── simulation.py        ←   Main orchestration
│
├── motor/                   ← Cable-robot motor model
│   ├── driver.py            ←   Single-axis crane lift simulation
│   ├── driver_multi.py      ←   5×5 m area coverage (121 positions)
│   ├── speed_control.py     ←   PWM duty cycle calculator
│   ├── concurrent.py        ←   Producer-consumer threading (no delay)
│   └── concurrent_rt.py     ←   Producer-consumer threading (real-time)
│
├── board/                   ← Raspberry Pi board-level utilities
│   ├── speed_control.py     ←   PWM motor speed control (RPi.GPIO)
│   └── test_led.py          ←   GPIO LED toggle test
│
├── hardware/                ← Real RPi deployment code
│   └── pi_main.py           ←   Full RPi implementation (PiCamera + RPi.GPIO)
│
├── assets/                  ← Project images
│   ├── robot_prototype.jpg  ←   Physical cable-robot prototype
│   ├── painting_output.jpg  ←   Actual painting output from robot
│   └── raspberry_pi_pinout.jpg
│
├── results/                 ← Sample simulation outputs
│   ├── sample_01/           ←   Wall detection + path for test run 1
│   └── sample_02/           ←   Wall detection + path for test run 2
│
├── docs/
│   ├── report/              ←   Project report (5510_DOC_1.docx)
│   ├── presentations/       ←   PRC2, PRC3, Final presentation
│   └── references/          ←   Research papers (PDFs)
│
├── .gitignore
├── requirements.txt
└── README.md
```

---

## Quick Start

### 1. Install dependencies
```bash
pip install -r requirements.txt
```

### 2. Run simulation (PC/laptop — uses webcam)
```bash
python main.py
```
This will:
- Open your webcam and capture a wall image
- Check for faces (aborts if found)
- Detect wall boundaries and estimate dimensions
- Generate and visualize the painting path
- Simulate motor + spray execution
- Save outputs as `wall_image_N.jpg`, `painting_path_N.png`, `painting_on_image_N.jpg`

### 3. Run modular simulation
```bash
python -m simulation.simulation
```

### 4. Run cable-robot motor model
```bash
python -m motor.driver_multi    # covers 5×5 m in 0.5 m steps
python -m motor.concurrent_rt   # concurrent path + speed control (real-time)
```

### 5. Deploy on Raspberry Pi
```bash
pip install RPi.GPIO picamera
python hardware/pi_main.py
```

---

## System Parameters

| Parameter | Value |
|-----------|-------|
| Steps per cm | 20 |
| Pixel to cm | 0.1 cm/pixel |
| Step size (normal) | 5 cm |
| Step size (narrow wall) | 1 cm |
| Spray duration | 0.5 s per position |
| PWM frequency | 100 Hz |
| Max motor speed | 1.0 m/s (PWM), 2.0 m/s (cable) |
| Fallback wall dims | 150 cm × 100 cm |
| Cable motor anchors | (0,5) m and (5,5) m |

## GPIO Pin Assignments (BCM)

| Pin | Function |
|-----|----------|
| GPIO 17 | STEP_X — X-axis stepper step |
| GPIO 27 | DIR_X  — X-axis direction |
| GPIO 22 | STEP_Y — Y-axis stepper step |
| GPIO 23 | DIR_Y  — Y-axis direction |
| GPIO 24 | SPRAY_PIN — DCV valve |
| GPIO 18 | PWM_PIN — speed control |

---

## Key Features

- **Face detection safety**: uses Haar cascade classifier; terminates with `SystemExit(1)` if a face is detected — no spray ever directed at a person
- **Modular architecture**: swap `MockGPIO`/`MockPiCamera` for real `RPi.GPIO`/`PiCamera` with zero logic changes
- **Auto-indexed outputs**: each run saves uniquely numbered files (no overwrites)
- **Cable-robot model**: dual-motor Euclidean positioning over a 5×5 m workspace
- **Threading**: producer-consumer pattern for concurrent path generation and speed control
- **Sustainable design**: built from repurposed materials (recycled containers, salvaged motors)

---

## Simulation Results (July 4, 2025)

- 13+ test runs executed
- Wall detection success rate: **85%** (fallback triggered in 2 runs)
- Path coverage completeness: **100%** in all runs
- Face detection safety: **100%** correct termination
- Cable model: **121/121** positions computed correctly

---

## License

Academic project — Project Code 5510, submitted to university.
