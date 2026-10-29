---
title: Software Proposal
---

# Software Proposal

## Overview

This page describes the planned logical behavior of the software running on each microcontroller in our system. Each subsystem has its own activity diagram, created by the team member responsible for it. The Master board coordinates the subsystems: it reads data from the LiDAR and cliff sensors, decides what to do, and then commands the motor driver and the spray system.

| Subsystem | Owner | Role |
| --- | --- | --- |
| Master | Owen C. | Starts the system, collects sensor data, and decides on motor and spray actions |
| LiDAR | Landon K. | Reads the LiDAR sensor and reports obstacle values to the Master |
| Cliff | Jaedyn H. | Checks for drop-offs and reports clearance to the Master |
| Motor Driver (MD) | Mimi N. | Compares current and target position and drives the motor |
| Spray | Thanh H. | Turns the sprayer on and off when signaled |

## Software Diagram

![Software activity diagrams for all subsystems](image/SoftwareProposal.png)
**Figure 1:** UML activity diagrams for the Master, LiDAR, Cliff, Motor Driver, and Spray microcontrollers. Click the image to enlarge.

Diagram source file: [SoftwareProposal.drawio](image/SoftwareProposal.drawio) (open it with [draw.io](https://app.diagrams.net) using *File → Open from → Device*).

## Subsystem Descriptions

### Master

- **Main:** After it starts, the Master initializes and then runs a continuous **Read → Think → Output** loop.
- **Initialize:** Turns on the HMI and establishes communication with the other boards. If initialization succeeds, it enters a ready state and starts up. If it fails, it enters an error state.
- **Read:** Collects the LiDAR string, the cliff sensor value, and the current motor position (Y).
- **Think:** Turns the LiDAR and cliff readings into true/false conditions. When both conditions are met, it sets the motor output value and actuates the spray.

### LiDAR

- **Main:** Initializes and then loops through **Read → Think → Output**.
- **Read:** Establishes communication with the LiDAR and repeatedly stores incoming LiDAR data.
- **Think:** Evaluates the LiDAR data as true or false and stores the resulting obstacle value.
- **Output:** Sends the obstacle value to the Master.

### Cliff

After start-up, the cliff board sends a signal and runs a clearance check. The result (true or false) is sent as the output, and the board loops back to send the next signal.

### Motor Driver (MD)

The motor driver reads the commanded position, compares the current position (x) with the target (y), and uses encoder output to keep updating that comparison. When the positions differ, it executes the move. It breaks out of the move once the target is reached, then loops back to read the next command.

### Spray

After start-up and initialization, the spray board waits for a signal from the Master. When the signal is true, it turns the sprayer on and keeps checking whether the signal is still active. When the signal goes false, it turns the sprayer off and returns to waiting for the next signal.

## Why We Structured the Software This Way

- **One diagram per microcontroller.** Our [block diagram](06-team-block-diagram.md) has five boards, so the software has five diagrams. Each team member designed the software for the board they own, which lets everyone develop and test their firmware separately before integration.
- **The same Read → Think → Output loop on every board.** Using one common structure makes every board's code predictable and easy to review. It also means the same debugging approach works on every board.
- **Every board loops forever.** No diagram ends in a dead end. After each pass, each board returns to the start of its loop, so the robot keeps working without a power cycle, and a board can recover from a bad reading on its next pass.
- **Decisions are made in one place.** Subsystem boards only report data (LiDAR, Cliff) or carry out commands (Motor Driver, Spray). Only the Master combines the sensor results and decides what to do. This keeps the safety logic in one place instead of spread across five boards.
- **Safety checks before action.** The Master only drives and sprays when both the LiDAR and Cliff checks are clear. If either check fails, the robot does not move or mark.
- **Safe start-up.** The Master confirms communication with the other boards during initialization. If that fails, it goes to an error state instead of driving with missing information.

## How the Software Meets Our Product Needs

| Product need (from [User Needs](04-%20User%20Needs%20and%20Benchmarking.md)) | How the software handles it |
| --- | --- |
| Detect and avoid obstacles (needs #1, #60) | The LiDAR board checks for obstacles on every loop, and the Master stops motion when one is detected. |
| Detect cliff edges to prevent fall-offs (need #65) | The Cliff board runs a clearance check on every loop, and the Master only drives when it is clear. |
| Place marks accurately (need #21) | The Motor Driver compares current and target position using encoder feedback until the target is reached. |
| Prevent spills and stray marks (need #29) | The Spray board only sprays while the Master's signal is active, and turns off as soon as it ends. |
| Clear diagnostic feedback when a fault occurs (need #58) | The Master enters an error state if communication fails during start-up. |
| Recover from communication problems (need #55) | Every board loops continuously, so a missed reading is replaced on the next pass. |
