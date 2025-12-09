# E-nose
<p align="center">
  <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/8/87/Microchip_Technology_logo.svg/2560px-Microchip_Technology_logo.svg.png" 
       alt="Microchip Logo" width="420"/>
</p>

<h1 align="center">E-Nose Classification System<br>Perfume • Coffee • Clean Air</h1>

<p align="center">Machine Learning on Adafruit Feather M4 • TensorFlow Lite Micro • Edge Impulse</p>

<p align="center">
  <img src="https://img.shields.io/badge/Platform-Edge%20Impulse-blue?style=for-the-badge" />
  <img src="https://img.shields.io/badge/MCU-Adafruit%20Feather%20M4-orange?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Accuracy-98.6%25-brightgreen?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Sensors-MQ%20Series-yellow?style=for-the-badge" />
  <img src="https://img.shields.io/badge/License-MIT-lightgrey?style=for-the-badge" />
</p>

---

# 🌫️ Project Overview

E-nose system capable of differentiating **perfume**, **coffee**, and **clean air** with 98% accuracy

This project uses an array of six MQ-series gas sensors combined with a lightweight machine learning model trained using **Edge Impulse**.  
The model is deployed on the **Adafruit Feather M4 Express**, which supports **TensorFlow Lite Micro** for efficient ARM-based inference.

---

# 🔧 Hardware Setup

## Sensor Array Used
The following sensors make up the current gas-detection array:

| Sensor | Primary Sensitivity |
|--------|----------------------|
| DF-NH3 | Ammonia |
| MQ-136 | Hydrogen sulfide |
| MQ-135 | VOCs / CO₂ |
| MQ-8 | Hydrogen |
| MQ-4 | Methane |
| MQ-2 | Smoke / LPG |

These sensors together form a basic but effective VOC fingerprint for classification.

## Wiring Diagram (Prototype on Feather M4 Express)

![Wiring Diagram](https://raw.githubusercontent.com/MicrochipTech/E-nose/main/diagram.png)

## Final MCU Choice: Adafruit Feather M4

Reasons:
- ARM Cortex-M4F with hardware floating-point accelerators  
- Fully supported by TensorFlow Lite Micro  
- Supported directly by Edge Impulse Arduino libraries  
- Higher performance vs AVR for ML workloads

![Feather M4](images/featherm4.png)

---

# 🧠 Model Training (Edge Impulse)

Project (public link):  
https://studio.edgeimpulse.com/public/661994/live

### Data Types Collected
- 6 analog voltage channels  
- Sampling frequency ~20 Hz  
- 5-second windows (~100 sample frames)  
- Labels: **Perfume**, **Coffee**, **Natural Air**

### Model Performance

- **99% accuracy on GPU (training)**
- **~97% estimated accuracy on Feather M4**
- Latency: **~5 seconds**
- Low overfitting risk (consistent validation performance)

```md
![Confusion Matrix](images/confusion-matrix.png)
```

---

# 📡 Data Collection Pipeline

### 1. Upload firmware to Arduino for sensor reading  
Source file:  
`collect-sensor-array.ino`  
:contentReference[oaicite:0]{index=0}

### 2. Log sensor values using Python
Script:  
`record-sensor-array.py`  


Run:

```bash
python record-sensor-array.py
```

This script:
- Reads serial output  
- Cleans & parses voltage values  
- Saves into a CSV dataset  

---

# 🧩 Edge Impulse Workflow

### Step 1 — Create a project  
Go to **studio.edgeimpulse.com** → *Create New Project*

### Step 2 — Import data  
**Data Acquisition → Upload Data**  
Upload CSV files generated from your Python logger.

### Step 3 — Build an impulse  
**Impulse Design → Create impulse**

Recommended:
- **Window size:** 5000 ms  
- **Frequency:** 20 Hz  
- **DSP Block:** Raw Data  
- **ML Block:** Classification (Neural Network)

### Step 4 — Train the model  
**Impulse Design → Classification**

Settings:
- Epochs: **100**
- Learning rate: **0.001**
- Train/Test Split: **80/20**

### Step 5 — Live testing  
**Live Classification → Serial (Feather M4)**

---

# 📥 Deployment to Adafruit Feather M4

### Step 1 — Export Arduino library  
Go to:

**Deployment → Arduino Library → TensorFlow Lite (int8)**  
Download the ZIP and place it under:

```
Documents/Arduino/libraries/
```

Restart Arduino IDE.

### Step 2 — Use the included inference sketch  

Inside the exported folder:

```
examples/arduino/feather-m4/feather-ei-inferencing.ino
```

This code:
- Reads analog data  
- Formats a feature vector  
- Passes it to the model  
- Prints probability scores  

### Typical Output

```
Coffee: 0.98
Perfume: 0.01
Natural Air: 0.00
```

---

# 🔭 Future Directions

- Add environmental compensation (temperature/humidity)
- Expand sensor suite: BMI, SPG, MISC chemical sensors
- Miniaturize system with custom PCB
- Add OLED display for portable operation
- Real-time continuous learning pipeline
- Expand classification to more VOC-heavy substances

(From your SIG slide notes)  
:contentReference[oaicite:2]{index=2}

---



# 📘 License
MIT License (modify if needed)

---

# 🙋‍♀️ Contact
Gokce (Chey) Yavuz  
December 2025  
E-Nose Project  
