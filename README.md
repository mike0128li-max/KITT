# KITT
KITT is an autonomous indoor delivery robot integrating edge AI, computer vision, SLAM navigation, person recognition, and embedded motor control for safe and intelligent delivery.

The long-term goal is simple:

**Give KITT a delivery task, let it navigate to the recipient, identify the correct person, securely deliver the item, and return autonomously.**

---

## Overview

KITT is built as a modular indoor service robot rather than a single-purpose RC car.

The system combines:

- Autonomous navigation and SLAM
- Edge-AI computer vision
- Person detection and tracking
- Face recognition
- Differential-drive motor control
- LiDAR and obstacle sensing
- Pan/tilt vision tracking
- Identity-verified delivery
- Secure delivery compartment
- Voice and human interaction
- Fail-safe and recovery systems

The project also serves as an experimental platform for studying **heterogeneous edge computing** — distributing tasks between specialized processors instead of forcing one computer to handle everything.

---

## System Architecture

KITT separates perception, high-level autonomy, and low-level control into different subsystems.

```text
                    ┌─────────────────────┐
                    │       KITT          │
                    │ Mission / Autonomy  │
                    └──────────┬──────────┘
                               │
              ┌────────────────┼────────────────┐
              │                │                │
              ▼                ▼                ▼

      ┌──────────────┐  ┌──────────────┐  ┌──────────────┐
      │    K230      │  │ Jetson Nano  │  │ Controller / │
      │ Edge Vision  │  │ Main Brain   │  │     MCU      │
      └──────┬───────┘  └──────┬───────┘  └──────┬───────┘
             │                 │                 │
     Person / Face      ROS / Navigation     Motor / Servo
       Tracking          SLAM / Planning       Control
             │                 │                 │
             └─────────────────┼─────────────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │   Physical Robot    │
                    │ Motors / Sensors /  │
                    │ Delivery Mechanism  │
                    └─────────────────────┘
```

### K230 — Vision System

The **Yahboom K230 AIMOTION2** acts as KITT's low-latency visual system.

Responsibilities include:

- Person detection
- Person tracking
- Face detection
- Face recognition
- Identity verification
- Pan/tilt camera tracking
- Future gesture recognition
- Future object recognition

The K230 can perform visual inference locally without sending every camera frame to the main computer.

---

### Jetson Nano — Main Computer

The Jetson Nano acts as the higher-level robotics computer.

Planned responsibilities include:

- ROS 2
- SLAM
- LiDAR processing
- Localization
- Path planning
- Autonomous navigation
- Mission logic
- Sensor fusion
- Obstacle avoidance
- Communication between subsystems

---

### Raspberry Pi 3B — Auxiliary Computer

A Raspberry Pi 3B is used as an auxiliary computing platform for experimentation and supporting services.

Possible responsibilities include:

- System monitoring
- Communication
- Robot services
- Networking
- Logging
- Experimental AI/agent integration

---

### MCU / Servo Controller — Real-Time Control

Low-level hardware control is separated from high-level AI processing.

Responsibilities include:

- Motor control
- PWM generation
- Servo control
- Encoder processing
- Watchdog functions
- Emergency stop behavior
- Hardware-level safety

This keeps time-sensitive control independent from slower AI workloads.

---

## KITT Vision

KITT currently uses a **K230-based pan/tilt vision system**.

The vision pipeline is designed around:

```text
Camera
  ↓
Person Detection
  ↓
Target Selection
  ↓
Target Position
  ↓
Pan / Tilt Tracking
  ↓
Face Recognition
  ↓
Identity Verification
```

One of the first milestones is:

### KITT Eyes

> **KITT should be able to see a person and continuously look at them.**

The tracking system uses a simple state-machine concept:

```text
SEARCH
   ↓
TRACK
   ↓
IDENTIFY
   ↓
INTERACT
```

If the target disappears, KITT can return to `SEARCH` and attempt to reacquire them.

---

## Navigation

KITT uses a two-wheel **differential-drive** architecture with a rear caster.

Navigation is being designed around:

- LiDAR
- Wheel encoders
- Ultrasonic sensing
- SLAM
- Localization
- Path planning
- Dynamic obstacle avoidance

A typical autonomous mission will eventually look like:

```text
Receive Mission
      ↓
Determine Destination
      ↓
Localize
      ↓
Plan Route
      ↓
Navigate
      ↓
Avoid Obstacles
      ↓
Search for Recipient
      ↓
Verify Recipient
      ↓
Deliver Package
      ↓
Return / Continue Mission
```

---

## Autonomous Delivery

The long-term goal of KITT is fully autonomous indoor delivery.

Example:

```text
"Deliver this package to Mike."
```

KITT should eventually be able to:

1. Accept the mission
2. Determine the destination
3. Generate a route
4. Navigate autonomously
5. Avoid obstacles
6. Search for the recipient
7. Detect the recipient
8. Verify their identity
9. Unlock the delivery compartment
10. Confirm delivery
11. Return to its starting point

Identity recognition and authorization are intentionally treated as separate steps.

Recognizing a person does **not automatically mean they are authorized** to access the payload.

---

## Hardware

Current and planned hardware includes:

| Component | Purpose |
|---|---|
| Yahboom K230 AIMOTION2 | Edge AI / computer vision |
| K230 pan/tilt camera | Active target tracking |
| Jetson Nano | Main robotics computer |
| Raspberry Pi 3B | Auxiliary computer |
| YDLIDAR | SLAM and obstacle detection |
| HC-SR04 | Close-range obstacle sensing |
| DC motors | Differential-drive movement |
| Motor driver | Motor power/control |
| Wheel encoders | Closed-loop movement |
| Servo controller | Pan/tilt and actuators |
| Servos | Camera and mechanical systems |
| Battery system | Mobile power |
| Delivery compartment | Secure payload storage |

KITT is intentionally modular so individual processors, sensors, and controllers can be replaced as the project evolves.

---

## Software

The project combines several areas of robotics and software engineering:

```text
Computer Vision
├── Person Detection
├── Face Detection
├── Face Recognition
└── Target Tracking

Robotics
├── ROS 2
├── SLAM
├── Localization
├── Path Planning
└── Navigation

Embedded Systems
├── UART Communication
├── PWM
├── Servo Control
├── Motor Control
└── Sensor Interfaces

AI
├── Edge Inference
├── Object Detection
├── Human Recognition
└── Future Agent Integration
```

Languages used or planned include:

- Python
- C / C++
- MicroPython
- ROS 2 interfaces

---

## Engineering Goals

KITT is not only about making a robot move.

The project is also intended to explore engineering questions such as:

### Heterogeneous Computing

Can dedicated edge-AI hardware reduce the workload of the main computer?

For example:

```text
Traditional Architecture

Camera
   ↓
Main Computer
   ↓
Detection + Navigation + Control


KITT Architecture

Camera
   ↓
K230
   ↓
Detection Result
   ↓
Main Computer
   ↓
Navigation
```

This allows different processors to specialize in different jobs.

Possible measurements include:

- Inference latency
- CPU usage
- Memory usage
- Power consumption
- Temperature
- Tracking accuracy
- Navigation reliability
- Mission success rate
- Recovery from failures

---

## Safety Philosophy

Autonomous robots should fail safely.

KITT is being designed around principles such as:

- Emergency stopping
- Communication timeouts
- Watchdog systems
- Safe motor states
- Lost-target recovery
- Navigation failure detection
- Manual override
- Identity verification before unlocking
- Modular subsystem isolation

If an important subsystem fails, the preferred behavior is:

```text
UNKNOWN STATE
      ↓
STOP
      ↓
DIAGNOSE
      ↓
RECOVER
      ↓
CONTINUE
```

rather than blindly continuing the mission.

---

## Project Status

KITT is currently **under active development**.

### Current Development

- [x] Overall system architecture
- [x] Differential-drive platform design
- [x] K230 vision platform
- [x] Face detection / recognition experiments
- [x] Person-tracking architecture
- [ ] Reliable K230 pan/tilt servo tracking
- [ ] Closed-loop motor control
- [ ] LiDAR integration
- [ ] ROS 2 integration
- [ ] SLAM
- [ ] Autonomous navigation
- [ ] Obstacle avoidance
- [ ] Recipient search
- [ ] Delivery compartment
- [ ] Identity-verified unlocking
- [ ] End-to-end autonomous delivery

---

## Final Goal

The final vision for KITT is a robot capable of completing a mission with minimal human intervention:

```text
MISSION
   ↓
PERCEIVE
   ↓
THINK
   ↓
NAVIGATE
   ↓
FIND
   ↓
VERIFY
   ↓
DELIVER
   ↓
RETURN
```

KITT is ultimately an experiment in building a complete autonomous system where **AI perception, robotics, embedded electronics, control systems, and software engineering meet in the physical world.**

---

## Why "KITT"?

The name is inspired by **KITT from Knight Rider** — an intelligent vehicle capable of sensing its environment, communicating with humans, and acting autonomously.

This project does not attempt to recreate the fictional vehicle directly.

Instead, it explores a more realistic question:

> **How close can a student-built robot get to becoming an intelligent, autonomous physical agent using today's hardware?**

---

## License

License information will be added as the project develops.

---

**KITT — See. Think. Navigate. Deliver.**
