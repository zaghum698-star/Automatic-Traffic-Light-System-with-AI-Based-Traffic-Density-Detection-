Here's the full README content as plain text — just copy and paste it into your `README.md` on GitHub:

```markdown
# Smart Traffic Light System — Density-Adaptive Signal Control

A density-adaptive traffic light controller for a 3-way intersection. A servo-mounted ESP32-CAM scans each approach road in turn, a vision pipeline (YOLOv7-tiny) counts the vehicles waiting on each one, and a second ESP32 uses that data to decide which road gets the green light and for how long — instead of cycling through a fixed timer regardless of actual traffic.

Built as an embedded systems and computer vision project targeting the traffic congestion caused by fixed-timer signals in dense urban intersections, with a strong focus on low cost and off-grid viability.

## How It Works

1. **Scan** — The ESP32 controller rotates an SG90 servo carrying the camera through three fixed angles, one per road, pausing briefly at each.
2. **Detect** — At each angle, a vehicle-detection model counts the vehicles present in frame.
3. **Report** — The vehicle count for each road is sent to the main ESP32 controller over UART or HTTP.
4. **Decide** — The controller identifies the road with the highest vehicle density and computes a green-light duration between 10 and 60 seconds, scaled to that density.
5. **Act** — The selected road turns green while the others remain red. A Finite State Machine keeps scanning, deciding, and switching signals in a safe, deterministic order.

## Repository Layout

```
.
├── firmware/
│   ├── esp32cam_vision_node/       # ESP32-CAM: video stream + vehicle-count sender
│   └── esp32_traffic_controller/   # ESP32: servo scan, FSM, LED signal control, web server
├── vision/
│   ├── vehicle_detection.py        # YOLOv7-tiny detection over the camera stream
│   └── requirements.txt
├── docs/
│   ├── report/                     # Full project report
│   └── images/                     # Block diagram, flowchart, results, hardware photos
├── LICENSE
└── .gitignore
```

## Hardware

| Component | Purpose |
|---|---|
| ESP32-CAM | Vision node — captures and streams video |
| ESP32 Dev Module | Main controller — servo, LEDs, decision logic |
| SG90 Servo Motor | Rotates the camera between the three roads |
| Red / Green LEDs (x3) | Traffic signal outputs |
| Resistors, breadboard, jumper wires | Supporting circuitry |
| 5V 2A power supply | Power source |

Full prototype cost: approximately PKR 4,500, with power draw under 2 W — affordable enough for municipal-scale deployment and compatible with solar or off-grid power.

## Software

- YOLOv7-tiny for vehicle detection
- Arduino/ESP-IDF (C++) firmware for both ESP32 boards
- Python (PyTorch, OpenCV) for the PC-side detection pipeline
- Libraries: ESP32Servo, HTTPClient/WebServer

## Getting Started

### 1. Flash the Firmware

Open `firmware/esp32cam_vision_node/esp32cam_vision_node.ino` and `firmware/esp32_traffic_controller/esp32_traffic_controller.ino` in the Arduino IDE (or PlatformIO). Set your WiFi credentials and the receiver IP/port at the top of each file, then flash each sketch to its respective board.

### 2. Run the Vision Pipeline

```bash
cd vision
pip install -r requirements.txt
python vehicle_detection.py
```

Update `ESP32_STREAM_URL` and `ESP32_POST_URL` in `vehicle_detection.py` to match your devices' IP addresses. Place a `yolov7-tiny.pt` weights file in the same directory (not included in this repository).

### 3. Wire It Up

Connect the LEDs and servo to the GPIO pins defined at the top of `esp32_traffic_controller.ino`, power both boards, and the system will begin scanning once connected to WiFi.

## Results

Testing against a fixed-timer baseline showed an average 32–42% reduction in waiting time and improved vehicle throughput, with detection accuracy above 85% across varying lighting conditions. Full results and comparison data are included in `docs/report/`.

## Future Work

- Emergency-vehicle detection and preemption
- Multi-intersection coordination over LoRa
- Solar power integration with a remote monitoring dashboard
- Weather-aware timing adjustment
- Migration to ESP32-S3 for on-chip acceleration

## License

This project is licensed under the MIT License — see the LICENSE file for details.
```

A couple of quick notes: replace the placeholder WiFi credentials/IPs in the actual `.ino` files before flashing (I already generalized those in the code I gave you), and if you'd like your name added as the author in the LICENSE file, just tell me and I'll update it.
