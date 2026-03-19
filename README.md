# Custom Two-Finger Underactuated Adaptive Gripper — MuJoCo Model

## ME5250 Modern Robotics — Spring 2026 Project 1

### Mechanism Overview

Each finger is a **planar parallelogram four-bar linkage** (option 4: underactuated hand):

```
     B -------- follower (L3=50mm) -------- D
     |                                      |
  L0=15mm                              L2=15mm (coupler)
  (ground)                                  |
     |                                      |
     A -------- driver (L1=50mm) ---------- C
                (proximal phalanx)    (coupler joint)
```

- **Ground link (L0 = 15mm)**: Horizontal distance between driver and follower pivots on the base
- **Driver link (L1 = 50mm)**: Proximal phalanx, actuated via tendon
- **Coupler link (L2 = 15mm)**: Connects driver tip to follower tip, carries the distal finger pad
- **Follower link (L3 = 50mm)**: Parallel outer link, constrained by four-bar

### Key Design Features

1. **Single-actuator tendon drive**: One position actuator controls a fixed tendon that symmetrically drives both fingers
2. **Parallelogram four-bar**: Distal pad maintains constant orientation (parallel jaw) during free motion
3. **Adaptive compliance**: Torsion spring at coupler joint + slightly soft equality constraint allows the distal pad to deviate and wrap around objects under contact forces
4. **Closed-chain modeling**: Four-bar loops modeled as tree structure + `<connect>` equality constraints

### Four-Bar Closure Verification

At zero joint angles (fingers fully open, pointing straight down):

| Point | Position (base frame) |
|-------|----------------------|
| A (right driver pivot) | (0.030, 0, -0.010) |
| B (right follower pivot) | (0.045, 0, -0.010) |
| C (driver tip) = A + (0, 0, -0.050) | (0.030, 0, -0.060) |
| D_coupler = C + (0.015, 0, 0) | **(0.045, 0, -0.060)** |
| D_follower = B + (0, 0, -0.050) | **(0.045, 0, -0.060)** |

**D_coupler = D_follower** → Four-bar closes exactly at zero angles. ✓

### Files

| File | Description |
|------|-------------|
| `adaptive_gripper.xml` | Gripper MJCF model (no scene elements) |
| `scene.xml` | Complete scene with gripper + ground + 3 test objects |
| `test_gripper.py` | Python script for simulation, data collection, and plotting |

### Quick Start

```bash
# 1. View in MuJoCo interactive viewer
python -m mujoco.viewer --mjcf scene.xml

# 2. Run headless test with analysis plots
python test_gripper.py

# 3. Run with interactive viewer
python test_gripper.py --viewer
```

### Kinematic Parameters

| Parameter | Value | Description |
|-----------|-------|-------------|
| L0 | 15 mm | Ground link (horizontal between pivots) |
| L1 | 50 mm | Driver / proximal phalanx |
| L2 | 15 mm | Coupler (kinematic link) |
| L3 | 50 mm | Follower (parallel link) |
| Opening gap | ~40 mm | Distance between proximal pads when open |
| Max closure angle | 0.8 rad (46°) | Per-finger rotation |
| Coupler spring stiffness | 3.0 N⋅m/rad | Adaptive compliance |
| Pad friction | 1.5 | High friction for secure grasping |

### DOF / Mobility Analysis

**Grübler's formula** (planar, per finger):
- M = 3(N-1) - 2J₁ = 3(4-1) - 2(4) = 9 - 8 = **1 DOF** per finger

Two fingers with tendon coupling: **1 DOF total** (single actuator)

The `<connect>` equality constraints remove the extra DOFs introduced by the tree representation, reducing the effective mechanism back to the correct 1-DOF behavior.

### MuJoCo Modeling Notes

- **Tree structure**: The four-bar is broken at the coupler-follower junction. The coupler is a child of the driver in the kinematic tree, and a `<connect>` constraint closes the loop.
- **Follower tip body**: A massless body at the follower's tip provides the second anchor point for the `<connect>` constraint.
- **Collision exclusions**: `<exclude>` elements prevent self-collision between linkage bodies that overlap geometrically.
- **Soft constraints**: `solref="0.005 1"` on the equality constraints keeps the four-bar stiff but allows slight deviation under high contact forces — enabling adaptive grasping.

### References

1. Birglen, Laliberté & Gosselin, *Underactuated Robotic Hands*, Springer, 2008
2. Dollar & Howe, "The Highly Adaptive SDM Hand", IJRR 29(5), 2010
3. Robotiq 2F-85 MuJoCo Menagerie model: github.com/google-deepmind/mujoco_menagerie/tree/main/robotiq_2f85
4. Yoon & Choi, "Underactuated Finger Mechanism Using Stackable Four-Bar Linkages", IEEE/ASME Trans. Mechatronics, 2017
