# Real-Time BCI EEG Player Movement Control (Unity + Python)

![BCI Demo](media/demo.gif)

This project connects real-time EXG/EEG signal acquisition with a 3D Unity runner game. Signals streamed via **Lab Streaming Layer (LSL)** are calibrated, detrended, and classified in Python, then transmitted via **UDP socket communication** to drive player actions in Unity in real time.

---

## 🏗️ System Architecture

```text
[EEG Hardware / Chords LSL Stream ("EXG")] 
                    │
                    ▼
[Python Processing Pipeline (main.py)]
   ├── DC Offset Removal & Detrending
   ├── Dynamic Threshold Calibration
   └── Refractory & Anti-Twitch Guard
                    │
            (UDP Socket: 5055)
                    ▼
[Unity Game Engine (BlinkReceiver.cs)] ➔ [Player Movement / Endless Runner]
```

### Action Mappings
* **Eye Blink:** Sends `jump` packet ➔ Triggers jump action in Unity.
* **Left Muscle Squeeze:** Sends `left` packet ➔ Steers player to the left lane.
* **Right Muscle Squeeze:** Sends `right` packet ➔ Steers player to the right lane.

---

## 📁 Repository Structure

```text
BCI_Project/
├── .gitignore                  # Excludes temporary Unity/Python build artifacts
├── README.md                   # Project documentation and execution instructions
├── requirements.txt            # Frozen Python dependencies
├── DriveLink.txt               # Link to full Unity scene and binary assets
│
├── python-backend/
│   └── main.py                 # LSL receiver, calibration, and UDP transmitter
│
└── unity-scripts/              # Standalone C# scripts for game mechanics
    ├── BlinkReceiver.cs        # Listens on UDP port 5055 and triggers actions
    ├── PlayerController3D.cs   # Player movement logic and lane switching
    ├── TileManager.cs          # Endless tile generation system
    ├── CameraFollow.cs         # Smooth camera tracking
    └── CoinRotate.cs           # Collectible animation logic
```

---

## 🛠️ Requirements & Environment Setup

### 1. Python Backend Setup
* **Python Version:** Python 3.10+ recommended

```bash
# Clone the repository
git clone https://github.com/YOUR_USERNAME/BCI_Project.git
cd BCI_Project

# Create and activate a virtual environment
python -m venv venv

# On Windows (PowerShell/CMD):
venv\Scripts\activate

# On Linux/macOS:
source venv/bin/activate

# Install required dependencies
pip install -r requirements.txt
```

### 2. Unity Engine Setup
* **Unity Version:** Compatible with Unity 2021.3 LTS / 2022.3 LTS or newer.
* **Full Unity Project & Assets:** Download the complete Unity project directory from the [Google Drive Link](https://drive.google.com/drive/folders/1Bvgt4bpL4ofao3DwP6X3r2JTaJSJqTx_?usp=sharing).

---

## 🚀 How to Run

1. **Start LSL Data Stream:**
   Ensure your EEG acquisition hardware or streaming software (e.g., Chords) is running and actively broadcasting an LSL stream with type `"EXG"`.

2. **Run Python Calibration & Signal Receiver:**
   ```bash
   python python-backend/main.py
   ```
   Follow the on-screen terminal prompts to perform the **Interactive Calibration Sequence**:
   * **Phase 1 (Resting Baseline):** Remain completely relaxed for 10 seconds to calculate signal DC offset and noise floor.
   * **Phase 2A & 2B (Muscle Squeezes):** Squeeze left and right muscles firmly for 10 seconds each to calibrate trigger sensitivity.
   * **Phase 2C (Eye Blinks):** Perform 10 distinct, deliberate eye blinks to compute blink peak detection thresholds.

3. **Launch Unity Game:**
   * Open the project in Unity Hub and load the main scene (`Assets/Scenes/`).
   * Press **Play** in Unity.
   * `BlinkReceiver.cs` will begin listening on `127.0.0.1:5055` and translate incoming socket payloads into player movement actions.