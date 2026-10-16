# Prompt as Machine Language (PAML)

## A Verified Skill-Orchestration Architecture for Conversational Industrial Automation

Prompt as Machine Language (PAML) is a conversational industrial-automation framework that translates natural-language instructions into validated machine operations. The user describes the intended task outcome, while the system generates and executes a structured plan using reusable machine skills.

PAML operates as a supervisory layer above conventional robot programs, G-code, PLC logic, and device commands. The large language model performs task interpretation and skill orchestration, while deterministic local controllers retain responsibility for real-time motion control, feedback, operating limits, interlocks, and emergency actions.

## System Architecture

The PAML workflow consists of:

1. Natural-language mission input
2. Machine-state and workspace grounding
3. Structured task-plan generation
4. Machine-skill selection and validation
5. Deterministic device execution
6. Physical-outcome verification
7. Bounded recovery when required

Two categories of machine skills are used:

- **Personal Machine Skills (PMS):** Control functions belonging to an individual device.
- **Collaborative Machine Skills (CMS):** Coordinated functions involving multiple devices, such as the camera, conveyor, and robot.

## Experimental Platform

The experimental workcell includes:

- Raspberry Pi 4 supervisory computer
- Motion 2350 Pro controller
- Raspberry Pi Pico/RP2350
- Encoder-controlled conveyor
- JGB37-3530-CE DC gearmotor
- USB camera
- Object-detection and color-classification system
- DOBOT MG400 robot
- Vacuum or mechanical gripping tool
- Object-sorting destination slots
- Proximity and limit sensors

The camera operates at approximately 30 Hz, while deterministic motor control is executed locally at up to 1 kHz.

## Experimental Tasks

Four mission-level tasks were used to evaluate natural-language interpretation, perception, semantic reasoning, physical manipulation, and instruction-scope compliance.

### Task 1: Rainbow Sorting

> Sort the objects according to their colors.

The system detects the colored objects, classifies their colors, picks each object, and places it into the corresponding destination slot.

**Experimental clip:**  
[Watch Task 1 on YouTube](https://youtu.be/iUUvlCWj8Zo)

### Task 2: Red-Object Selection

> Pick only the red objects from the conveyor.

This task evaluates color-specific target selection and the rejection of non-target objects.

**Experimental clip:**  
[Watch Task 2 on YouTube](https://youtu.be/yQhA4gYm9BQ)

### Task 3: Different-Color Object Removal

> The color of the first object is the default color. Pick the different-color object out of the conveyor.

The first detected object establishes the reference color. The system then compares subsequent objects with this reference and removes the object having a different color.

**Experimental clip:**  
[Watch Task 3 on YouTube](https://youtu.be/No8gmMPyzco)

### Task 4: Warm-Tone Object Selection

> Pick the first three warm-tone color objects.

This task evaluates semantic color reasoning, ordered target selection, and compliance with the requested quantity limit.

**Experimental clip:**  
[Watch Task 4 on YouTube](https://youtu.be/AFbtzuOHLpM)

## Evaluation Metrics

The system is evaluated using:

- Prompt-to-plan latency
- Clarification burden
- Task-interpretation accuracy
- Object-detection rate
- Semantic color-classification accuracy
- Picking success rate
- Sorting and placement accuracy
- Semantic-correction burden
- Target-selection accuracy
- Non-target rejection accuracy
- Instruction-scope compliance
- End-to-end task success rate
- Failure attribution

## Key Contribution

The principal contribution of PAML is an intent-level machine-programming abstraction that combines:

- Natural-language task specification
- State-grounded interpretation
- Reusable and validated machine skills
- Explicit machine-authority boundaries
- Deterministic local execution
- Physical-outcome verification
- Failure attribution and bounded recovery

The language model does not directly control actuators or execute safety-critical feedback loops. Its role is limited to supervisory reasoning, task planning, and skill orchestration.

## Research Status

This repository supports the experimental implementation and journal paper:

> **Prompt as Machine Language: A Verified Skill-Orchestration Architecture for Conversational Industrial Automation**

The project is currently under experimental evaluation and manuscript development.

## Authors

- Ruj Samarnworakit
- Torpat Sirilattaporn
- Ratchatin Chancharoen

Department of Mechanical Engineering  
Faculty of Engineering  
Chulalongkorn University  
Bangkok, Thailand

## License

The license for this project will be specified in the `LICENSE` file.

## Citation

If you use this project in academic work, please cite the associated publication after its bibliographic information becomes available.
