# 🤖 Conversational Automation

## A Verified Skill-Orchestration Architecture for Conversational Industrial Automation

> **Natural-language task specification with validated machine-skill orchestration and deterministic physical execution.**

[Project Image / System Overview]

**[📄 Paper] | [🎬 Demonstration Videos] | [💻 Source Code] | [📚 Documentation]**

---

## 🧠 1. PAML Overview

**Prompt as Machine Language (PAML)** is a conversational industrial-automation framework that translates natural-language instructions into validated machine operations. Instead of requiring the user to directly program robot trajectories, G-code commands, or device-specific control logic, PAML allows the intended task outcome to be expressed in natural language. The system interprets the instruction, generates a structured task plan, selects appropriate machine skills, coordinates the machines in the workcell, and verifies whether the intended physical outcome has been achieved.

PAML operates as a supervisory layer above conventional robot programs, G-code, PLC logic, and embedded device commands. The large language model is responsible for high-level reasoning, task decomposition, semantic interpretation, and machine-skill orchestration, while deterministic local controllers remain responsible for real-time motion control, feedback loops, operating limits, interlocks, and emergency actions. This separation establishes a clear machine-authority boundary between language-based supervisory reasoning and safety-critical device execution.

---

## 🏗️ 2. System Architecture

### 2.1 Overall Architecture

The PAML architecture connects the user, the language-model-based supervisory layer, the machine-skill registry, deterministic local controllers, and the physical automation workcell. A user instruction is first interpreted in the context of the available machine state and workspace information. The supervisory system then generates a structured execution plan, selects validated machine skills, and coordinates their execution through the corresponding device controllers. The final physical outcome is observed and verified before the mission is considered complete.

4.png

### 2.2 Machine-Skill Categories

PAML organizes machine capabilities into two principal categories: **Personal Machine Skills (PMS)** and **Collaborative Machine Skills (CMS)**. Personal Machine Skills represent deterministic functions belonging to an individual machine or device, such as detecting an object with a camera, moving a conveyor, or picking an object with a robot. Collaborative Machine Skills coordinate several Personal Machine Skills across multiple devices to achieve a higher-level physical task, such as detecting, classifying, transporting, picking, sorting, and verifying an object within a single mission.

### 2.3 Machines in the Automation Workcell

The experimental workcell is organized around four primary subsystems that represent the main functional players commonly found in modern automation cells: (a) an edge supervisory computer, (b) a machine-vision system, (c) a conveyor system, and (d) a robot manipulation system. Together, these subsystems cover the core functions of decision-making, perception, material transport, and physical manipulation. The edge computer performs supervisory reasoning and coordinates the overall task, the vision system provides environmental perception and physical-outcome verification, the conveyor manages object transport and positioning, and the robot executes manipulation and sorting actions. This configuration therefore provides a representative automation-cell platform for evaluating machine-skill orchestration across heterogeneous devices.

### 2.4 Experimental Platform

The experimental platform is a laboratory-scale automation workcell developed for research and education. It integrates the key elements of a modern automation cell and serves as a testbed for PAML and next-generation automation concepts.

![PAML System Architecture](Figures/4.png)

---

## ⚙️ 3. Machines in the Workcell

### 💻 3.1 Edge Supervisory Computer

The supervisory layer is implemented on a Raspberry Pi 4 and provides the computational interface between the user instruction and the physical machines. It hosts the Codex/LLM interface, task planner, machine-skill registry, skill validator, execution monitor, and recovery manager. Its role is to interpret the user prompt, ground the requested mission against the current machine state, decompose the mission into executable operations, select and validate available skills, sequence the required actions, monitor execution, assess task completion, and initiate physical verification or bounded recovery when necessary.

### 👁️ 3.2 Machine-Vision System

The machine-vision subsystem uses a USB camera together with computer-vision functions for object detection, localization, color classification, target identification, and physical-outcome verification. The camera operates at approximately **30 Hz**, providing visual information for both task execution and post-action verification. Its Personal Machine Skills include detecting and locating objects, classifying object colors, identifying target objects, comparing object attributes, verifying object presence or removal, and confirming the final sorting outcome.

The vision functions are primarily implemented using [OpenCV](https://github.com/opencv/opencv) and [OpenCV Contrib](https://github.com/opencv/opencv_contrib).

### 🏭 3.3 Conveyor System

The conveyor system provides controlled transportation and positioning of objects within the workcell. It is based on a Motion 2350 Pro controller with a Raspberry Pi Pico/RP2350, a JGB37-3530-CE DC gearmotor, encoder feedback, lead-screw transmission, proximity sensors, a limit switch, and a local motion controller. Deterministic motor control is executed locally at up to approximately **1 kHz**, allowing timing-sensitive control to remain independent of the supervisory language model.

The conveyor exposes reusable Personal Machine Skills for starting and stopping motion, moving forward or backward, moving to a sensor position, indexing object position, homing the mechanism, and positioning an object for robot pickup. Low-level movement can be represented through deterministic command interfaces such as [G-code](https://reprap.org/wiki/G-code).

### 🦾 3.4 Robot System

The manipulation subsystem is based on a DOBOT MG400 robot equipped with a vacuum or mechanical gripping tool and predefined object-sorting destinations. The robot is responsible for physical pick-and-place operations after an object has been identified and positioned by the preceding subsystems. Its Personal Machine Skills include moving to an object, picking and lifting the object, moving to a specified destination, placing and releasing the object, returning to a standby configuration, and executing predefined manipulation sequences.

The robot interface follows the deterministic command structure defined by the DOBOT MG400 platform and its associated `MG400-QUICKSTART-GUIDE.pdf` documentation.

---

## 🧩 4. Machine-Skill Architecture

### 4.1 Skill Definition

A machine skill represents a reusable and validated execution capability exposed to the PAML supervisory layer. Each skill defines its name, category, description, required input parameters, preconditions, machine-state requirements, execution command, expected output, completion condition, error condition, and verification method. This formal structure allows the supervisory layer to reason about machine capabilities without directly controlling the underlying actuator-level implementation.

### 4.2 Personal Machine Skills

Personal Machine Skills encapsulate deterministic operations belonging to a single machine or device. Examples include detecting an object, classifying its color, moving the conveyor, homing an axis, picking an object, or placing an object. These skills form the lowest reusable execution units exposed to PAML and provide a controlled interface between high-level task planning and deterministic machine execution.

### 4.3 Collaborative Machine Skills

Collaborative Machine Skills combine multiple Personal Machine Skills across several devices to complete a higher-level operation. For example, an object-sorting task may require the conveyor to transport an object, the camera to detect and classify it, the conveyor to position it at the pickup location, the robot to pick and place it, and the vision system to verify the resulting placement. PAML coordinates these individual machine capabilities as a single mission-level sequence.

### 4.4 Machine Authority Boundary

PAML maintains a deliberate separation between supervisory intelligence and machine-level control. The language model may determine what task should be performed, which available skills are appropriate, how they should be sequenced, and whether the resulting outcome satisfies the requested intent. However, the language model does not directly perform motor-current regulation, encoder feedback control, hard real-time motion control, safety interlocking, or emergency-stop handling. These responsibilities remain with deterministic local controllers.

---

## 🧪 5. Demonstration Cases

Four mission-level tasks were designed to evaluate the ability of PAML to interpret natural-language instructions, perform semantic reasoning, coordinate perception and manipulation, and comply with task scope.

### 🌈 5.1 Task 1 — Rainbow Sorting

> **Prompt:** Sort the objects according to their colors.

This task evaluates complete color-based object detection, classification, manipulation, and sorting. The system detects each object on the conveyor, determines its color, selects the corresponding destination, coordinates the robot pick-and-place operation, and places the object into the appropriate sorting slot.

[▶️ Watch Task 1 on YouTube](https://youtu.be/iUUvlCWj8Zo)

### 🔴 5.2 Task 2 — Red-Object Selection

> **Prompt:** Pick only the red objects from the conveyor.

This task evaluates semantic target selection and non-target rejection. The system identifies red objects as valid manipulation targets while allowing objects of other colors to pass without being picked. The mission therefore requires not only correct perception but also compliance with the semantic restriction expressed by the word **only**.

[▶️ Watch Task 2 on YouTube](https://youtu.be/yQhA4gYm9BQ)

### 🔍 5.3 Task 3 — Different-Color Object Removal

> **Prompt:** The color of the first object is the default color. Pick the different-color object out of the conveyor.

This task evaluates reference-based reasoning and contextual comparison. The first detected object establishes the reference color for the mission. Subsequent objects are compared against this dynamically established reference, and an object having a different color is identified as the target for removal. The task therefore requires the system to derive a mission-specific rule from the observed physical state rather than relying on a predefined target color.

[▶️ Watch Task 3 on YouTube](https://youtu.be/No8gmMPyzco)

### 🔥 5.4 Task 4 — Warm-Tone Object Selection

> **Prompt:** Pick the first three warm-tone color objects.

This task evaluates semantic category interpretation, sequential target selection, and quantity compliance. The system must interpret the abstract category **warm tone**, determine which observed colors belong to that category, preserve the order in which matching objects appear, and terminate the selection process after the first three valid objects have been picked. The mission therefore combines semantic reasoning with explicit scope control.

[▶️ Watch Task 4 on YouTube](https://youtu.be/AFbtzuOHLpM)

---

## 📂 6. Repository Structure

The repository is organized to separate reusable machine skills, software modules, experimental data, documentation, media, and publication-related material. Machine-specific and collaborative skills are stored under `machine-skills/`, while planning, vision, skill-registry, and machine-interface software are maintained under `software/`. Experimental procedures, execution logs, and results are grouped by mission under `experiments/`. Supporting documentation, figures, photographs, videos, and publication material are stored separately to simplify reuse and reproducibility.

```text id="zgk6y6"
PAML/
├── README.md
├── LICENSE
├── machine-skills/
│   ├── vision/
│   ├── conveyor/
│   ├── robot/
│   └── collaborative/
├── software/
│   ├── planner/
│   ├── skill-registry/
│   ├── vision/
│   └── machine-interface/
├── experiments/
│   ├── task-1-rainbow-sorting/
│   ├── task-2-red-selection/
│   ├── task-3-different-color/
│   └── task-4-warm-tone/
├── docs/
├── media/
└── publications/
```

---

## 📚 7. References and Dependencies

The PAML implementation relies on established software and machine-control interfaces. Computer-vision functions are supported by [OpenCV](https://github.com/opencv/opencv) and [OpenCV Contrib](https://github.com/opencv/opencv_contrib), while deterministic motion commands can be expressed through conventional [G-code](https://reprap.org/wiki/G-code) interfaces. Robot manipulation is implemented using the DOBOT MG400 platform and its associated control documentation.

---

## 👥 8. Main Contributors

The PAML experimental platform and research implementation were developed by **Ruj Samarnworakit**, **Torpat Sirilattaporn**, and **Ratchatin Chancharoen** at the Department of Mechanical Engineering, Faculty of Engineering, Chulalongkorn University, Bangkok, Thailand.

---

## 📑 9. Citation

**To be added upon publication.**

---

## ⚖️ 10. License

**To be added.**

---

## ✉️ 11. Contact

For technical questions, research discussions, or potential collaboration related to PAML, please contact the project contributors through the Department of Mechanical Engineering, Faculty of Engineering, Chulalongkorn University.
