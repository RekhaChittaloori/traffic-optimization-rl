# 🚦 Dynamic Traffic Light Management System

<div align="center">

![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![PyTorch](https://img.shields.io/badge/PyTorch-EE4C2C?style=for-the-badge&logo=pytorch&logoColor=white)
![SUMO](https://img.shields.io/badge/SUMO-Simulation-00A550?style=for-the-badge&logo=opensourceinitiative&logoColor=white)
![Deep Q-Learning](https://img.shields.io/badge/Deep%20Q--Learning-DQN-8A2BE2?style=for-the-badge)
![Arduino](https://img.shields.io/badge/Arduino-Hardware-00979D?style=for-the-badge&logo=arduino&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-yellow?style=for-the-badge)

> 🎓 **College Major Project** — B.Tech CSE, Sreenidhi Institute of Science and Technology, Hyderabad
> 
> *An intelligent, decentralized traffic signal control system using Deep Q-Networks (DQN) and SUMO simulation — trained across multiple city maps to minimize vehicle waiting time in real-time.*

</div>

---

## 📌 Table of Contents
- [Overview](#-overview)
- [City Grid Concept](#-city-grid-concept)
- [How It Works](#-how-it-works)
- [Training Process](#-training-process)
- [Tech Stack](#-tech-stack)
- [Maps Used](#-maps-used)
- [Results](#-results)
- [Live Simulation Demo](#-live-simulation-demo)
- [Arduino Integration](#-arduino-integration)
- [Installation & Usage](#-installation--usage)
- [Project Structure](#-project-structure)
- [Author](#-author)

---

## 🔍 Overview

Traditional traffic lights use **fixed timer cycles** — they don't adapt to real-time vehicle density. This wastes time and fuel, especially in high-traffic urban areas.

This project uses **Deep Q-Learning (DQN)** to build an agent that:
- Observes vehicle count on each side of every junction
- Dynamically decides **which lane gets the green signal**
- Minimizes total vehicle waiting time across **all junctions simultaneously**
- Works on multiple city map layouts

---

## 🏙️ City Grid Concept

The model works on a city grid with multiple traffic light nodes (n1, n2, n3, n4...).

![City Grid](documentation/samplecity1.PNG)

- Each node has **4 sides** (lanes)
- The agent makes **one decision per node** — which side gets the green light
- A **minimum green time** is enforced (e.g. 30 seconds) to prevent rapid switching
- Objective: **minimize total vehicle waiting time** across all nodes

---

## ⚙️ How It Works

```
🚗 Vehicles enter SUMO simulation
        ↓
📡 Agent observes: vehicles per lane (4 values × number of junctions)
        ↓
🧠 DQN selects action: which lane gets the green signal
        ↓
🚦 TraCI API updates the traffic light phases
        ↓
📊 Reward = −1 × total vehicle waiting time
        ↓
🔁 Agent learns and improves across epochs
```

### 🧠 RL Details

| Component | Value |
|---|---|
| **Algorithm** | Deep Q-Network (DQN) |
| **State** | Vehicle count per lane (4 per junction) |
| **Action** | Select 1 of 4 signal phases per junction |
| **Reward** | Negative total waiting time |
| **Optimizer** | Adam (lr = 0.1) |
| **Loss Function** | Mean Squared Error (MSE) |
| **Exploration** | ε-greedy with decay (ε_dec = 5e-4, min = 0.05) |
| **Discount Factor γ** | 0.99 |
| **Hidden Layers** | 2 × 256 neurons (ReLU) |

---

## 🔄 Training Process

![Training Loop](documentation/train_loop.png)

- Training uses **fixed events** (pseudo-random vehicle routes) — not random — to ensure reproducible, comparable results
- Multiple fixed events are used so the model learns to handle **diverse traffic situations**
- Each epoch runs for a fixed number of simulation steps
- The **best model** (lowest total waiting time) is saved automatically

---

## 🛠️ Tech Stack

| Technology | Purpose |
|---|---|
| **Python** | Core language |
| **PyTorch** | Deep Q-Network (DQN) model |
| **SUMO** | Traffic simulation environment |
| **TraCI API** | Real-time SUMO control via Python |
| **NumPy** | State/memory array management |
| **Matplotlib** | Epoch vs time training plots |
| **Arduino** | Physical traffic light hardware (optional) |

---

## 🗺️ Maps Used

Three different city maps were used to train and evaluate the model:

### Map 1
![Map 1](maps_images/city2.JPG)

### Map 2
![Map 2](maps_images/city3.JPG)

### Map 3
![Map 3](maps_images/citymap.JPG)

---

## 📊 Results

Training graphs showing **total waiting time decreasing across epochs**:

### Epoch vs Time — Map 1
![Plot 1](plots/time_vs_epoch_city1.png)

### Epoch vs Time — Map 2
![Plot 2](plots/time_vs_epoch_city3.png)

### Epoch vs Time — Map 3
![Plot 3](plots/time_vs_epoch_model.png)

> 📉 Consistent downward trend confirms the agent is successfully learning to reduce traffic congestion over time.

---

## 🎬 Live Simulation Demo

https://user-images.githubusercontent.com/44360315/113673665-e8edd300-96d6-11eb-8fbe-d09e078fbfbe.mp4

> The trained model running on SUMO-GUI — watch the agent dynamically control signal phases based on live vehicle counts.

---

## 🔌 Arduino Integration

The simulation is connected to a physical Arduino board to control real LED traffic lights — bridging software simulation and hardware.

> ⚠️ Arduino integration currently supports **single crossroad only**. Multi-junction support coming soon.

### Arduino Setup
<img src="arduino_images/arduino1.jpg" width="600" height="400"/>
<img src="arduino_images/arduino2.jpg" width="600" height="400"/>
<img src="arduino_images/arduino3.jpg" width="600" height="400"/>

---

## 🚀 Installation & Usage

### Prerequisites
- Python 3.8+
- [SUMO GUI](https://sumo.dlr.de/docs/Downloads.php)
- PyTorch, NumPy, Matplotlib, pyserial

### Step 1 — Install dependencies
```bash
git clone https://github.com/RekhaChittaloori/traffic-optimization-rl.git
cd traffic-optimization-rl
pip install -r requirements.txt
```

### Step 2 — Create network and route files
```bash
cd maps
python randomTrips.py -n network.net.xml -r routes.rou.xml -e 500
```

### Step 3 — Set SUMO configuration
Edit `configuration.sumocfg`:
```xml
<input>
  <net-file value='maps/city1.net.xml'/>
  <route-files value='maps/city1.rou.xml'/>
</input>
```

### Step 4 — Train the model
```bash
python train.py --train -e 50 -m model_name -s 500
```

### Step 5 — Run trained model (with GUI)
```bash
python train.py -m model_name -s 500
```

### Step 6 — Run with Arduino
```bash
python train.py -m model_name -s 500 --ard
```

### 📋 All CLI Options

| Flag | Description | Default |
|---|---|---|
| `--train` | Enable training mode | False |
| `-m` | Model name to save/load | `model` |
| `-e` | Number of training epochs | 50 |
| `-s` | Steps per simulation epoch | 500 |
| `--ard` | Enable Arduino hardware | False |

---

## 📁 Project Structure

```
traffic-optimization-rl/
│
├── train.py                        # Main DQN training & testing script
├── configuration.sumocfg           # SUMO simulation config
├── requirements.txt
│
├── maps/                           # SUMO network & route files
│   ├── city1.net.xml
│   ├── city1.rou.xml
│   └── tripinfo.xml
│
├── models/                         # Saved PyTorch model weights (.bin)
├── plots/                          # time_vs_epoch training graphs
├── maps_images/                    # Screenshots of city maps
├── arduino_images/                 # Arduino hardware photos
└── documentation/                  # Architecture diagrams
```

---

## 🔭 Future Scope

- [ ] Cooperative multi-agent learning across junctions
- [ ] Real city maps via OpenStreetMap
- [ ] Pedestrian crossing signal support
- [ ] Compare DQN with PPO / A3C algorithms
- [ ] Deploy on Raspberry Pi for edge computing

---

## 👩‍💻 Author

**Chittaloori Rekha**  
B.Tech CSE — Sreenidhi Institute of Science and Technology, Hyderabad

[![LinkedIn](https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/rekha-chittaloori-b21b56278/)
[![GitHub](https://img.shields.io/badge/GitHub-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/RekhaChittaloori)
[![LeetCode](https://img.shields.io/badge/LeetCode-FFA116?style=for-the-badge&logo=leetcode&logoColor=black)](https://leetcode.com/u/Rekha8/)

---

<div align="center">
  <i>⭐ If you found this project useful, please consider giving it a star!</i>
</div>
