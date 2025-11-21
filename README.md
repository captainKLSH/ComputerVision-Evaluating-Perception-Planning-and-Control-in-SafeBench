# ComputerVision-Evaluating-Perception-Planning-and-Control-in-SafeBench

Platform: CARLA Simulator & SafeBench (hosted on AWS)

Focus: Object Detection Metrics, Environment Setup, Reinforcement Learning (SAC)

## 🚀 Project Challenges

#### Challenge 1: Perception Evaluation

- Goal: Implement metrics to grade object detection models.

- Input: Bounding boxes (x1, y1, x2, y2), confidence scores, and class labels.

- Models: Comparison between One-Stage (YOLOv5) and Two-Stage (Faster R-CNN) detectors.

- Metric: You must calculate Average Precision (AP). This involves plotting Precision-Recall (PR) curves at different "strictness" levels (IoU thresholds from 0.5 to 0.9).

#### Challenge 2: Environment Setup

- Goal: Configure a robust simulation environment on the cloud.

- Hardware: AWS EC2 g5.xlarge instance (GPU-accelerated).

- Software: Ubuntu 22.04, NVIDIA Drivers, TurboVNC (for viewing the simulator), and CARLA.

#### Challenge 3: Planning & Control

- Goal: Train and evaluate driving agents in SafeBench.

- Rule-Based: Tune PID controllers for lateral (steering) and longitudinal (gas/brake) control.

- Reinforcement Learning (SAC): Train a Soft Actor-Critic agent in ordinary traffic scenarios, then test its robustness in accident-prone scenarios.

## 🛠️ Environment Setup Guide (AWS & CARLA)

Because this project requires heavy GPU usage, we use Amazon Web Services (AWS).

#### Step 1: AWS Launch

1. Request Limits: Ensure you have vCPU limits approved for "G instances" in US East (N. Virginia).
2. Launch Instance:
- OS: Ubuntu 22.04
- Type: g5.xlarge
- Key Pair: Download and save your .pem file safely.

#### Step 2: System Configuration

Log in via SSH:
```ssh -i <your_key.pem> ubuntu@<your_instance_ip>```

Install Drivers:
The cloud computer doesn't come with GPU drivers installed.
```bash
sudo apt-get install nvidia-driver-525
# REBOOT INSTANCE AFTER THIS STEP via AWS Console
```

#### Step 3: GUI & Remote Desktop

To "see" the simulator, we need a desktop interface.
```bash
# Install Ubuntu Desktop
sudo apt-get update
sudo apt-get install ubuntu-desktop

# Install TurboVNC (Remote Desktop)
wget [https://sourceforge.net/projects/turbovnc/files/3.0.3/turbovnc_3.0.3_amd64.deb/download](https://sourceforge.net/projects/turbovnc/files/3.0.3/turbovnc_3.0.3_amd64.deb/download)
sudo dpkg -i download
```
#### Step 4: SafeBench & CARLA

- Install Miniconda:
```bash
wget [https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh](https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh)
bash Miniconda3-latest-Linux-x86_64.sh
```

- Install CARLA: Upload the CARLA tarball from your local machine to the server using ```scp```, then extract it.

- Configure Paths: Add export ```CARLA_ROOT=...``` to your ```~/.bashrc.```

## 📝 Usage & Commands

### 1. Perception Metrics (Challenge 1)

Implementation details in _perception/eval.py_ (hypothetical path).
Generate PR curves for IoU thresholds *0.5:0.1:0.9*.

### 2. Running the Simulator (Challenge 2)

Start CARLA in off-screen mode (renders to the GPU buffer, viewable via VNC):
```./CarlaUE4.sh -prefernvidia -RenderOffScreen -carla-port=2000```


### 3. Training & Evaluation (Challenge 3)

Evaluate Rule-Based Agent:

```python scripts/run.py --agent_cfg basic.yaml```

Train SAC Agent (Ordinary Scenarios):

```python scripts/run.py --agent_cfg sac.yaml--scenario_cfg ordinary.yaml \--num_scenario 4 --mode train_agent```

Model checkpoints are saved to: 
***Safebench/safebench/agent/model_ckpt/sac***

Evaluate SAC Agent (Safety-Critical Scenarios):

```python scripts/run.py --agent_cfg sac.yaml --scenario_cfg standard.yaml \--num_scenario 4 --mode eval --save_video```


