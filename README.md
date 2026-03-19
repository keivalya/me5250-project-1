# 1-DOF Grasp-and-Twist Underactuated Gripper

MuJoCo simulation for **ME5250 Project 1**. This project studies **one single mechanism**: a tendon-coupled robotic gripper that closes on an object first, then redirects the same actuator input into passive wrist rotation.

![Mechanism overview](figures/fig_screenshots.png)

## What It Is

- **1 actuator**
- **4 mechanical DOFs**
- **2 four-bar fingers**
- **2 passive distal joints**
- **1 passive wrist joint**

The mechanism is underactuated: the fingers, distal links, and wrist do not all need separate motors. The structure and contact mechanics do the switching.

![Phase transition](figures/fig_phase_transition_annotated.png)

## Quick Start

```bash
conda create -n me5250-gripper python=3.10 -y # create a conda environment

conda activate me5250-gripper # activate it

python -m pip install mujoco numpy # install mujoco + basic runtime deps

python -m mujoco.viewer --mjcf grasp_twist_scene.xml # launch the scene in the MuJoCo
```

## Repo Map

- `grasp_twist_gripper.xml`: final MuJoCo mechanism used in the report
- `report.txt`: LaTeX source for the final writeup
- `report_figures.py`: regenerates the report figures
- `capture_project_video_shots.py`: records presentation-ready MuJoCo clips
- `make_screenshots.py`: generates screenshot collage assets

## Main Results

- Sequential grasp-to-twist behavior from one actuator
- Passive adaptation across **cylinder**, **sphere**, and **box**
- Closed-chain four-bar modeling in MuJoCo using `connect` constraints
- Reliable cylindrical grasp range of **8 mm to 36 mm**

![Multi-shape behavior](figures/fig_multishape_compound.png)
