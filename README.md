# AI-Powered Multi-Agent Reinforcement Learning Traffic Control System

This project is an advanced, production-grade, AI-driven traffic signal control system. It integrates computer vision (**YOLOv8**), Multi-Agent Reinforcement Learning (**MARL** with **PPO**), and physical edge computing (**ESP32-CAM** and **ESP32 Microcontroller**) to dynamically optimize traffic flow at a 4-way intersection, featuring emergency vehicle preemption.

---

<p align="center">
  <img src="https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white" alt="Python">
  <img src="https://img.shields.io/badge/YOLOv8-111F68?style=for-the-badge&logo=ultralytics&logoColor=white" alt="YOLOv8">
  <img src="https://img.shields.io/badge/OpenCV-5C3EE8?style=for-the-badge&logo=opencv&logoColor=white" alt="OpenCV">
  <img src="https://img.shields.io/badge/Stable--Baselines3-8A2BE2?style=for-the-badge" alt="Stable-Baselines3">
  <img src="https://img.shields.io/badge/PyTorch-EE4C2C?style=for-the-badge&logo=pytorch&logoColor=white" alt="PyTorch">
  <img src="https://img.shields.io/badge/Gymnasium-0081A5?style=for-the-badge" alt="Gymnasium">
  <img src="https://img.shields.io/badge/SUMO-00A86B?style=for-the-badge" alt="SUMO">
  <img src="https://img.shields.io/badge/Flask-222222?style=for-the-badge&logo=flask&logoColor=white" alt="Flask">
  <img src="https://img.shields.io/badge/HTML5-E34F26?style=for-the-badge&logo=html5&logoColor=white" alt="HTML5">
  <img src="https://img.shields.io/badge/CSS3-1572B6?style=for-the-badge&logo=css3&logoColor=white" alt="CSS3">
  <img src="https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black" alt="JavaScript">
  <img src="https://img.shields.io/badge/ESP32-E7352C?style=for-the-badge&logo=espressif&logoColor=white" alt="ESP32">
  <img src="https://img.shields.io/badge/MQTT-660066?style=for-the-badge&logo=eclipsemosquitto&logoColor=white" alt="MQTT">
  <img src="https://img.shields.io/badge/Serial_Communication-555555?style=for-the-badge" alt="Serial Communication">
  <img src="https://img.shields.io/badge/Git-F05032?style=for-the-badge&logo=git&logoColor=white" alt="Git">
  <img src="https://img.shields.io/badge/GitHub-181717?style=for-the-badge&logo=github&logoColor=white" alt="GitHub">
</p>


## 📌 Overview

Traditional traffic signal systems often rely on fixed timing schedules that cannot respond effectively to changing traffic conditions.

This project introduces an **AI-powered adaptive traffic control system** that detects vehicles, analyzes traffic conditions, and dynamically adjusts signal timings using Reinforcement Learning. It also supports emergency vehicle prioritization and physical traffic light control through ESP32 hardware.

The system integrates camera-based perception, intelligent decision-making, traffic simulation, and IoT-based actuation into a unified architecture.


## 🛠️ Tech Stack


| Category | Technologies |
|---|---|
| Programming | Python |
| Computer Vision | YOLOv8, OpenCV |
| Reinforcement Learning | PPO, Stable-Baselines3, Multi-Agent RL |
| Simulation | SUMO |
| Backend | Flask, Python |
| Frontend | HTML5, CSS3, JavaScript |
| Hardware | ESP32-CAM, ESP32 Development Board |
| Communication | Wi-Fi, HTTP, Serial Communication, MQTT (where configured) |
| Development Tools | VS Code, Git, GitHub |


## 🎥 System Demonstration

Watch the complete working demonstration of the traffic control system in action:

[📁 View Demonstration Video (Complete_Working_video.mp4)](https://www.youtube.com/watch?v=A8SE4DjzUhE)



This video showcases:
* **Real-time ESP32-CAM HTTP snapshot capture** from four direction lanes (North, East, South, West).
* **YOLOv8 object detection and vehicle tracking** running on live frames.
* **Cooperative PPO MARL agent decision-making** and dynamic green time calculation.
* **Bidirectional serial communication** with the ESP32 microcontroller actuating physical traffic light signals.

## 🖥️ Project Screenshots

Showcase the most important parts of your system here.

### Live Traffic Monitoring Dashboard

<p align="center">
  <img src="docs/images/dashboard.png" alt="Live Traffic Monitoring Dashboard" width="90%">
</p>

### Vehicle Detection with YOLOv8

<p align="center">
  <img src="docs/images/vehicle-detection.png" alt="YOLOv8 Vehicle Detection" width="90%">
</p>

### Emergency Vehicle Priority

<p align="center">
  <img src="docs/images/emergency-priority.png" alt="Emergency Vehicle Priority Demonstration" width="90%">
</p>

### Physical Traffic Light Prototype

<p align="center">
  <img src="docs/images/hardware-prototype.jpg" alt="ESP32 Traffic Light Hardware Prototype" width="90%">
</p>

---

## 🏗️ System Architecture

The project consists of three main components: **Perception** (vehicle detection from live HTTP video feeds), **Decision-Making** (reinforcement learning or emergency rule override), and **Actuation** (serial communication to the ESP32 physical traffic lights).

<img width="1932" height="2768" alt="mermaid-diagram" src="https://github.com/user-attachments/assets/17d1dccf-73ac-4caf-9764-3fcd7fb842d3" />

## 🚀 Key Components & Features

### 1. Computer Vision Perception (`yolov8/`)
* **Custom YOLOv8 Model (`best1.pt`):** Custom-trained to detect and classify standard vehicles (cars, trucks, buses, motorcycles) and emergency vehicles (ambulances, fire trucks, police cars).
* **Multi-Camera Management (`camera_stream.py`):** Spawns concurrent background threads to grab frames via HTTP snapshot endpoints (`/capture`, `/jpg`) or MJPEG streams from 4 independent ESP32-CAM modules.
* **Vehicle Tracking & Metric Extraction (`yolo_detector.py`):** Employs YOLOv8 tracking (with persistent track IDs) to monitor vehicle movements. It calculates:
  * **Vehicle Count:** The number of active vehicles in each lane.
  * **Queue Length:** Vehicles static or moving below a velocity threshold.
  * **Average Speed Estimate:** Pixel distance shifts mapped to approximate real-world speed ($m/s$).
  * **Emergency Detection:** Real-time flagging of emergency vehicle presence.

### 2. Multi-Agent Reinforcement Learning (MARL) (`rl/`)
* **PPO MARL Model (`ppo_marl_v3.zip`):** Developed using Stable-Baselines3. The intersection operates as a Multi-Agent environment where each lane's signal behaves as an agent cooperating to minimize global intersection wait times.
* **Observation Space (22-Dimensional):**
  * Current active signal phase index (normalized).
  * Current phase elapsed duration (normalized, capped at 120s).
  * Queue lengths (normalized by 30) for North, East, South, West.
  * Vehicle counts (normalized by 40) for North, East, South, West.
  * Cumulative waiting times (normalized by 500) for North, East, South, West.
  * Vehicle densities (normalized by 0.15) for North, East, South, West.
  * Average speeds (normalized by 15.0) for North, East, South, West.
* **Action Space:** Continuous/discrete decisions to `KEEP` the current green phase active or `SWITCH` to the lane with the highest waiting queue.
* **Sumo Simulation Training (`sumo_marl_env_v3.py`):** Trained in **SUMO** (Simulation of Urban MObility) with TraCI over thousands of cycles to handle asymmetric traffic peaks, reward throughput, and penalize queue build-ups.

### 3. Emergency Preemption Override
* Features a rule-based safety override. If YOLOv8 detects an emergency vehicle in any lane, the system bypasses the PPO model's normal decision-making and immediately switches the signal to **GREEN** for that specific lane for 20 seconds, preventing delays.

### 4. ESP32 Microcontroller Actuator (`esp32_traffic_controller/`)
* Runs non-blocking firmware on an ESP32 microcontroller to drive physical traffic light LEDs (Red, Yellow, Green for North, East, South, West).
* **Bidirectional Serial Protocol (115200 Baud):**
  * **Incoming commands:** Single-character priority lane commands (e.g., `N\n`, `E\n`, `S\n`, `W\n`) or full sequential cycle timings (e.g., `N:15,E:20,S:10,W:15\n`).
  * **Outgoing status:** Responds with state updates (`GREEN: N`, `YELLOW: N`) and reports `READY` upon completing a sequence, signaling the Python system to capture the next set of camera feeds.

### 5. Flask Live Monitoring Dashboard (`app.py`)
* Web-based interface displaying the annotated camera frames with YOLOv8 bounding boxes.
* Shows current vehicle counts, calculated green times, active signal states, and logs of RL actions.
* Allows comparisons between fixed-time scheduling, Stable RL control, and RL with emergency preemption overrides in the SUMO simulation.

---

## 📂 Project Directory Structure

```
├── app.py                      # Flask web application dashboard
├── traffic_system.py           # Production integration pipeline (runs cam -> detection -> serial)
├── traffic_controller.py       # Core controller class linking YOLOv8, MARL, and cameras
├── requirements.txt            # Python dependencies
├── .gitignore                  # Git ignore rules (configured for clean repo commits)
│
├── yolov8/                     # YOLOv8 assets and modules
│   ├── best1.pt                # Custom trained YOLOv8 model weights
│   ├── yolo_detector.py        # Vehicle detector with persistent object tracking
│   └── camera_stream.py        # Multithreaded ESP32-CAM HTTP/MJPEG stream manager
│
├── rl/                         # Reinforcement Learning modules
│   ├── models/                 # Saved models
│   │   └── ppo_marl_v3.zip     # Production cooperating PPO MARL model
│   ├── sumo_marl_env_v3.py     # Custom Gymnasium environment with SUMO and TraCI
│   ├── train_marl_v3.py        # MARL training pipeline
│   └── sim_runner.py           # Runs fixed-time vs RL simulation modes for evaluation
│
├── sumo/                       # SUMO simulation networks
│   └── stage8_asymmetric/      # Intersection network topology, routes, configurations
│
├── esp32_traffic_controller/
│   └── esp32_traffic_controller.ino  # Non-blocking firmware for ESP32 light actuation
│
├── templates/
│   └── index.html              # Frontend live dashboard layout
│
└── detection_results/          # Evaluation results
    ├── cycle216_N.jpg          # Sample detection output - North lane (216th cycle)
    ├── cycle216_E.jpg          # Sample detection output - East lane (216th cycle)
    ├── cycle216_S.jpg          # Sample detection output - South lane (216th cycle)
    └── cycle216_W.jpg          # Sample detection output - West lane (216th cycle)
```

---


### Simulation and Reinforcement Learning

The system also uses **SUMO** to simulate traffic scenarios and evaluate adaptive traffic control. The PPO-based multi-agent setup uses a shared policy across the configured traffic agents.

<p align="center">
  <img src="docs/images/rl-training-results.png" alt="Reinforcement Learning Training Results" width="85%">
</p>

*Optional: Replace the image above with your PPO training reward graph, SUMO simulation screenshot, or traffic performance comparison.*

> **Implementation note:** This diagram represents the intended high-level architecture. Update the communication paths and components to match your actual implementation—for example, include MQTT only where it is genuinely used, and show SUMO as a separate simulation environment if it is not directly connected to the physical controller during live operation.


### 🔄 Workflow

1. **Data Capture:** ESP32-CAM devices capture traffic footage from different approaches.
2. **Vehicle Detection:** YOLOv8 processes frames to detect vehicles and supported emergency vehicle classes.
3. **Traffic Analysis:** The system derives traffic metrics such as vehicle counts and queue lengths.
4. **Intelligent Decision-Making:** The PPO-based multi-agent controller selects signal actions based on the observed traffic state.
5. **Emergency Preemption:** Emergency detection triggers the configured priority override.
6. **Signal Actuation:** The Python controller communicates with the ESP32 to control physical traffic light LEDs.
7. **Live Monitoring:** The dashboard displays available camera streams and traffic metrics.

## ⚙️ Getting Started

## 🛠️ Installation & Setup

### 1. Prerequisites
1. **SUMO:** Download and install [SUMO](https://eclipse.dev/sumo/) on your system. Make sure you set the `SUMO_HOME` environment variable:
   * **Windows:** Add `C:\Program Files (x86)\Eclipse\Sumo` to your path and environment variables.
2. **Python:** Python 3.8 - 3.10 is recommended (for Stable-Baselines3 and PyTorch compatibility).

### 2. Install Dependencies
```bash
pip install -r requirements.txt
```

### 3. ESP32 Hardware Assembly
1. **Cameras:** Flash standard streaming/snapshot firmware on 4 ESP32-CAMs. Update their IP addresses in `yolov8/camera_stream.py` and `traffic_system.py`.
2. **Controller:** Open `esp32_traffic_controller/esp32_traffic_controller.ino` in Arduino IDE. Connect the LEDs to the mapped GPIO pins, flash the ESP32, and note its COM port (default: `COM7`).

---

## 🏃 Running the System

### 1. Flask Live Dashboard
To start the live web dashboard interface:
```bash
python app.py
```
Open `http://localhost:5000` in your web browser. You can trigger simulation comparisons or launch the live camera control mode.

### 2. Production Traffic Actuator (Command Line)
To run the automated loop connecting real cameras, model inference, and the serial ESP32 microcontroller directly:
```bash
python traffic_system.py --port COM7 --conf 0.3
```

### 3. Test Controller offline (Synthetic Mode)
To verify model loading, detection, and decision-making logic without physical hardware connected:
```bash
python traffic_controller.py --test
```
This runs the controller for 20 cycles using synthetic video frames and displays dynamic green allocation.


## 🔌 Hardware Setup

The physical implementation uses ESP32-based hardware to connect camera capture and traffic signal control with the Python application.

| Component | Purpose |
|---|---|
| ESP32-CAM | Capture traffic footage |
| ESP32 Development Board | Control traffic light outputs |
| Traffic Light LEDs | Represent intersection signal states |
| Laptop / Host Computer | Run vehicle detection, RL control, and monitoring |
| USB Serial Connection | Exchange control commands and status messages |

<p align="center">
  <img src="docs/images/hardware-setup.png" alt="Hardware Wiring and Setup" width="85%">
</p>

**Important:** Verify the GPIO mapping, serial protocol, and signal transition logic against your actual hardware implementation before running the controller.

## 📊 Results and Evaluation

Use this section to present measured results from your SUMO simulations or hardware tests.

| Metric | Result |
|---|---|
| Average Waiting Time | `ADD MEASURED RESULT` |
| Average Queue Length | `ADD MEASURED RESULT` |
| Average Vehicle Throughput | `ADD MEASURED RESULT` |
| Emergency Detection Performance | `ADD MEASURED RESULT` |
| Comparison with Fixed-Time Signals | `ADD MEASURED RESULT` |

<p align="center">
  <img src="docs/images/performance-comparison.png" alt="Traffic Control Performance Comparison" width="90%">
</p>

> Include only results you have measured. Document the simulation settings, test duration, traffic demand, and baseline used for fair comparison.

## 🚀 Future Enhancements

- Improve coordination between neighboring intersections.
- Expand emergency vehicle detection and priority handling.
- Evaluate the controller under different traffic demand scenarios.
- Add historical analytics and performance visualizations to the dashboard.
- Improve fault handling and communication reliability between the host and ESP32.
- Evaluate the system under larger simulated road networks.

## 🤝 Contributing

Contributions, suggestions, and improvements are welcome.

1. Fork the repository.
2. Create a feature branch.
3. Commit your changes.
4. Open a pull request describing your contribution.

## 📜 License

This project is currently provided for educational and research purposes.

Choose and add an appropriate open-source license before permitting reuse. For example, you can add an MIT License by creating a `LICENSE` file.

## 👨‍💻 Author

**Midhun Kunjumon**

B.Tech in Computer Science and Engineering — Artificial Intelligence and Machine Learning


---

<p align="center">
  If you find this project interesting, consider giving the repository a ⭐
</p>
