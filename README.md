# Automatic LEGO Distribution System

An automated, edge-computing material sorting system driven by a distributed micro-controller network and embedded computer vision. Designed and built at the Free University of Tbilisi.

---

## System Demo

[![Automatic Lego Distribution System Demo](https://img.youtube.com/vi/in9Zvj4_v-k/hqdefault.jpg)](https://youtu.be/in9Zvj4_v-k)

*Click the image above to watch the full system sorting demonstration on YouTube.*

---

## Key Performance Metrics

* **Sorting Accuracy:** >85% overall sorting accuracy under controlled ambient conditions.
* **Throughput:** ~5 seconds per brick (~12 bricks per minute).
* **Architecture:** 3-node wireless micro-controller network operating over ESP-NOW and HTTP JPEG streaming.
* **Indexing Mechanism:** 5-bucket symmetrical carousel driven by a NEMA 17 stepper motor indexed at 72° (40 full steps) per bucket shift.

---

## Distributed System Architecture

The processing load is distributed across three independent microcontrollers to optimize real-time responsiveness and mitigate compute bottlenecks:

1. **ESP32 CYD Controller (`master.ino`):** Dedicated Cheap Yellow Display node driven over VSPI via `TFT_eSPI` and an XPT2046 touch controller. Manages the touchscreen user interface and sends operational modes wirelessly via ESP-NOW.
2. **ESP32 Main Controller (`esp32_code.ino`):** Serves as the central state machine and motion control hub. Monitors an array of 3 active-LOW IR beam-break sensors, controls conveyor belt timing, and executes step/dir commands for the stepper motor drivers.
3. **ESP32-CAM (Vision Sensor Node):** Captures 160x120 JPEG frames and streams them over HTTP to perform localized image analysis in PSRAM.

---

## Power Electronics & Hardware Architecture

* **Power Delivery Isolation:** Powered by a 12V DC main supply. High-torque motor coils (NEMA 17 via A4988 drivers) run off 12V, while a DC-DC buck converter steps voltage down to 5V for logic.
* **Noise Mitigation:** The ESP32-CAM is powered through an isolated delivery topology to prevent motor switching transients from dropping the supply voltage, corrupting ADC reads, or triggering brownout resets.
* **CAD & Mechanical Design:** Features a custom-geared conveyor belt and 5-slot indexing carousel designed in Autodesk Fusion 360 and manufactured via laser-cut plywood and 3D printing.

---

## Embedded Computer Vision Pipeline

The system uses dynamic PSRAM frame processing (RGB565) to classify bricks by shape and color:

1. **Color & Saturation Thresholding:** Isolates pixel blobs from the dark conveyor belt background using luminance/saturation filters (R + G + B <= 60, V >= 35%, S >= 28%).
2. **Dual-Pass Shape Classification:**
   * **Pass 1 (Area Infill Ratio):** Calculates and compares blob area infill ratios against bounding rectangle, circumscribed circle, and maximum triangle models.
   * **Pass 2 (Contour Vector Extraction):** Traces perimeters and calculates internal corner angles via vector dot products to extract valid vertices (>50°). Combined voting resolves geometric ambiguity.

---

## Repository Structure

```text
├── Project Report      # Full technical research & project report
├── README.md           # Project overview landing page
├── esp32_code.ino      # ESP32 sorting logic, motor actuation & IR sensor code
└── master.ino          # ESP32 CYD touchscreen UI firmware
