---
title: Product Requirements
---

# Product Requirements

## Project Objective

> **Objective:** Build a working prototype of OpenField, an open-source autonomous robot that drives over a construction floor or job site and sprays layout marks where the plan says they belong. The prototype should show that an affordable, modular robot can mark a layout safely and repeatably, as a low-cost alternative to commercial layout robots that cost tens of thousands of dollars.

## Stakeholders

| **Stakeholder** | **What they need from OpenField** |
| --- | --- |
| Operators (layout crews, builders) | A robot that is quick to set up, marks accurately, and is safe to run around other workers. |
| Maintainers | Modular parts that are easy to diagnose, swap, and reprint. |
| Firmware Leads | Open, well-documented firmware they can extend. |
| Hardware Leads | Standard connectors, off-the-shelf parts, and published design files. |
| Construction companies (buyers) | A much lower cost than commercial layout robots, with no subscription. |

See [User Needs and Benchmarking](04-%20User%20Needs%20and%20Benchmarking.md) for where each stakeholder's voice came from.

## Use Cases

### Use Case 1: Marking wall lines on a new home's slab

1. A layout crew member carries OpenField onto a finished concrete slab and sets it at the starting point.
2. The operator powers it on. The Master board starts every subsystem and shows **Ready** on the LCD. If a board does not respond, the LCD shows an error instead.
3. The operator uses the dial to start the layout.
4. OpenField drives along the planned lines. The LiDAR board watches for obstacles, and the Cliff board watches for drop-offs, such as an open stairwell.
5. While the robot is on a planned line, the Master tells the Spray board to mark the floor.
6. If a worker steps in front of the robot, it stops and waits. It continues once the path is clear.
7. When the layout is done, the LCD shows **Complete**, and the crew starts framing walls on the marked lines.

### Use Case 2: Marking on rough ground before the slab is poured

1. A crew needs reference marks on a graded dirt or grass site, before the foundation is poured.
2. OpenField's treads keep traction on loose and uneven ground, where a wheeled robot would slip.
3. The robot marks the planned points while the Cliff board keeps it away from trenches and excavation edges.
4. At the end of the day, a maintainer finds the spray actuator worn. Because each subsystem plugs into the Master with the same ribbon connector, they unplug the Spray board, replace the part, and plug it back in without touching the other boards.

## Design Aspects

### Hardware / Product Design

- Tracked (tread) chassis for traction on concrete, dirt, gravel, and grass.
- Five PIC18F57Q43 Curiosity Nano boards: a Master plus LiDAR, Cliff, Motor Driver, and Spray subsystems (see the [Team Block Diagram](06-team-block-diagram.md)).
- Each subsystem connects to the Master through an 8-pin ribbon cable.
- 3D-printed body parts with off-the-shelf fasteners.

### Software / Functionality

- Every board runs a continuous Read → Think → Output loop (see the [Software Proposal](07-Software%20Proposal.md)).
- Only the Master makes decisions. Subsystem boards report sensor data or carry out commands.
- The robot only drives and sprays when the LiDAR and Cliff checks are both clear.

### Interactivity and User Experience

- An LCD shows the robot's state (ready, running, complete, error) and low-battery warnings.
- A dial lets the operator start, pause, and select options without a laptop.
- Clear error messages tell the operator which subsystem has a problem.

### Customization

- Open-source firmware and design files, so users can change behavior or add features.
- Modular subsystem boards on standard connectors, so new tools (such as a different marker) can be swapped in.

### Manufacturing

- Off-the-shelf development boards, sensors, and fasteners.
- 3D-printed parts that can be reprinted when broken.
- Design files published so others can build their own.

### Safety

- Obstacle detection with the LiDAR, and drop-off detection with the cliff sensors.
- An emergency stop that halts all motion and spraying.
- Safe start-up: the robot will not move if any subsystem fails to respond.
- The sprayer only runs while the Master's signal is active.

## Requirement Criteria Specifications

Each requirement has a measurable target, the user need it comes from, and how we will verify it. Need numbers (#) refer to our [75 user needs](04-%20User%20Needs%20and%20Benchmarking.md#our-75-user-needs), and B-numbers refer to needs found in benchmarking.

**Verification methods:** **Test** (measured trial), **Demonstration** (shown working), **Inspection** (checked by looking or measuring), **Analysis** (calculated or checked against datasheets).

### Performance

| ID | Requirement | Target | Source Need | Verification |
| --- | --- | --- | --- | --- |
| PR-1 | Marking accuracy | Each mark within **±3 cm** of its planned position over a 3 m × 3 m test area | #21, B23 | **Test:** Mark 10 planned points, then measure each one's error from the reference with a tape measure. |
| PR-2 | Battery runtime | At least **4 hours** of continuous operation | #31 | **Test:** Run continuously from a full charge until shutdown. |
| PR-3 | Low-battery warning | LCD warning at or below **20%** battery | #35 | **Demonstration:** Run the battery down and confirm the warning appears. |

> **Why ±3 cm:** Commercial layout robots reach 1/16 inch (about 1.6 mm), but they rely on laser trackers or robotic total stations that cost thousands of dollars ([benchmarking](04-%20User%20Needs%20and%20Benchmarking.md#voice-of-the-customer-benchmarking)). Our prototype positions itself with LiDAR and motor encoders, so ±3 cm is a realistic target for this stage. Reaching the 1/16-inch tolerance in need #21 remains a long-term goal.

### Mobility

| ID | Requirement | Target | Source Need | Verification |
| --- | --- | --- | --- | --- |
| PR-4 | Climb inclines | Drives up slopes of at least **2.5°** without slipping | #14 | **Test:** Drive up a ramp set to 2.5°. |
| PR-5 | Cross rough floors | Drives over bumps up to **2 cm** high | #15 | **Test:** Drive over a 2 cm board. |
| PR-6 | Fit through doorways | Total width of **30 in (76 cm)** or less | #42 | **Inspection:** Measure the robot's width. |
| PR-7 | One-person carry | Total weight of **50 lb (22.7 kg)** or less | #43 | **Inspection:** Weigh the robot. |

### Environmental

| ID | Requirement | Target | Source Need | Verification |
| --- | --- | --- | --- | --- |
| PR-8 | Operating temperature | Operates from **14 °F to 122 °F** (−10 °C to 50 °C) | #12, B14 | **Analysis:** Check every component's rated temperature range on its datasheet. |

### Safety

| ID | Requirement | Target | Source Need | Verification |
| --- | --- | --- | --- | --- |
| PR-9 | Obstacle stop | Stops at least **30 cm** before an obstacle in its path | #1, #60 | **Test:** Place an object in the path, 10 trials. Measure the stopping distance each time. |
| PR-10 | Drop-off stop | Stops before any drop of more than **5 cm**, with no part of the treads past the edge | #3, #65 | **Test:** Drive toward a table edge, 10 trials. |
| PR-11 | Emergency stop | Halts all motion and spraying within **1 second** | #61 | **Test:** Press the stop and time how long it takes the robot to halt. |
| PR-12 | Spray control | Sprayer turns off within **0.5 seconds** after the Master's spray signal ends | #29 | **Test:** Time the delay between the signal ending and the spray stopping. |
| PR-13 | Safe start-up | Robot does not move if any subsystem fails to respond during start-up | #64 | **Demonstration:** Unplug one board before power-on, and confirm the robot stays still and shows an error. |

### Software and Communication

| ID | Requirement | Target | Source Need | Verification |
| --- | --- | --- | --- | --- |
| PR-14 | Detect lost boards | Master detects a disconnected subsystem within **1 second** and names it on the LCD | #55, #58 | **Demonstration:** Unplug a ribbon cable during operation. |
| PR-15 | Status display | LCD shows the current state: **ready, running, complete, or error** | #56, #62 | **Demonstration:** Step through each state. |

### Manufacturing and Customization

| ID | Requirement | Target | Source Need | Verification |
| --- | --- | --- | --- | --- |
| PR-16 | Standard parts | All fasteners are standard off-the-shelf sizes, and all custom parts can be 3D printed | #68, #71 | **Inspection:** Review the bill of materials. |
| PR-17 | Swappable subsystems | Any subsystem board can be unplugged and replaced in **under 5 minutes** without tools | #50, #70 | **Test:** Time a board swap. |
| PR-18 | Open source | Firmware and design files are published on GitHub | #75 | **Inspection:** Check the public repositories. |

## Open Questions

1. **Positioning:** Can LiDAR and motor encoders alone hold ±3 cm over a full layout, or will we need extra reference markers or a total station?
2. **Tread slip:** How much do the treads slip on smooth concrete, and how much does that hurt accuracy?
3. **Marking medium:** Which spray paint or marker works best on both concrete and dirt, and how do we keep it from clogging?
4. **Loading the plan:** How will layouts get onto the robot? Right now, the operator only has the LCD and dial.
5. **Power:** What battery type and capacity do we need to reach 4 hours, given the motors' current draw? Each teammate's power budget will help answer this.
6. **Cost:** What is a realistic total parts cost for the prototype?
