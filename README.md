# Proposal

## Project Title
**Microwatt-SMP: Smart Mobility Pre-Processor for the Microwatt OpenPOWER Core (SKY130)**

## Abstract
This project extends the Microwatt POWER core with a Smart Mobility Pre-Processor (SMP) accelerator designed for real-time sensor workloads. Connected through the Wishbone bus, the accelerator performs noise filtering, sensor fusion, and anomaly detection on GPS, IMU, and speed data. It produces compact mobility frames that reduce CPU workload and latency.

Implemented in RTL and verified with testbenches, the design will be synthesized on SKY130 using the OpenLane flow. All design files and configurations are open-sourced to ensure reproducibility. The aim is to demonstrate a practical, domain-specific hardware extension of Microwatt that improves efficiency and safety in smart city and intelligent transport applications.

## Motivation
Microwatt provides a clean, functional implementation of the POWER ISA, but its current SoC design processes sensor workloads entirely in software, causing inefficiencies in latency, power consumption, and bandwidth.  

Edge computing systems, particularly in mobility and transport, require domain-specific accelerators that preprocess data closer to the source. The Smart Mobility Pre-Processor fills this gap by offloading filtering and fusion tasks to dedicated hardware, reducing CPU overhead while maintaining ISA compliance and integration simplicity.

## Design Features
- **Filtering:** FIR and moving average noise suppression.
- **Sensor Fusion:** Combines GPS, IMU, and speed data into unified mobility states.
- **Decision Logic:** Detects unsafe speed changes and lane deviations.
- **Dual Modes:** 
  - Eco (filter-only)
  - Safety (fusion + anomaly detection)
- **Wishbone Interface:** Memory-mapped registers for Microwatt CPU control.
- **SKY130 Standard Cell Implementation:** OpenLane reproducible flow.

## Integration
- Implemented as a Wishbone peripheral in the Microwatt SoC.
- Controlled via C firmware compiled with GCC for POWER.
- ISA preserved; only memory-mapped register interface added.
- Verified through CPU + accelerator co-simulation.

## Deliverables
- RTL modules: `smp_top.v`, `fusion_logic.v`, `filter_core.v`, `wb_wrapper.v`.
- Unit and system testbenches with Python golden models (NumPy).
- Example firmware demonstrating UART log output of mobility states.
- OpenLane SKY130 configs, synthesis, and STA results.
- Documentation, diagrams, and reproducibility scripts.
- Demo video and screenshots for final submission.

## Impact
- **Practical:** Improves safety and efficiency for smart mobility and IoT edge workloads.
- **Community Value:** Provides a reusable accelerator block for OpenPOWER projects.
- **Innovation:** Demonstrates domain-driven heterogeneous computing.
- **Reproducibility:** Fully open-source and compliant with ChipFoundry challenge rules.

# OpenFrame Overview

The OpenFrame Project provides an empty harness chip that differs significantly from the Caravel and Caravan designs. Unlike Caravel and Caravan, which include integrated SoCs and additional features, OpenFrame offers only the essential padframe, providing users with a clean slate for their custom designs.

<img width="256" alt="Screenshot 2024-06-24 at 12 53 39 PM" src="https://github.com/efabless/openframe_timer_example/assets/67271180/ff58b58b-b9c8-4d5e-b9bc-bf344355fa80">

## Key Characteristics of OpenFrame

1. **Minimalist Design:** 
   - No integrated SoC or additional circuitry.
   - Only includes the padframe, a power-on-reset circuit, and a digital ROM containing the 32-bit project ID.

2. **Padframe Compatibility:**
   - The padframe design and pin placements match those of the Caravel and Caravan chips, ensuring compatibility and ease of transition between designs.
   - Pin types are identical, with power and ground pins positioned similarly and the same power domains available.

3. **Flexibility:**
   - Provides full access to all GPIO controls.
   - Maximizes the user project area, allowing for greater customization and integration of alternative SoCs or user-specific projects at the same hierarchy level.

4. **Simplified I/O:**
   - Pins that previously connected to CPU functions (e.g., flash controller interface, SPI interface, UART) are now repurposed as general-purpose I/O, offering flexibility for various applications.

The OpenFrame harness is ideal for those looking to implement custom SoCs or integrate user projects without the constraints of an existing SoC.

## Features

1. 44 configurable GPIOs.
2. User area of approximately 15mm².
3. Supports digital, analog, or mixed-signal designs.

# openframe_timer_example

This example implements a simple timer and connects it to the GPIOs.

## Installation and Setup

First, clone the repository:

```bash
git clone https://github.com/efabless/openframe_timer_example.git
cd openframe_timer_example
```

Then, download all dependencies:

```bash
make setup
```

## Hardening the Design

In this example, we will harden the timer. You will need to harden your own design similarly.

```bash
make user_proj_timer
```

Once you have hardened your design, integrate it into the OpenFrame wrapper:

```bash
make openframe_project_wrapper
```

## Important Notes

1. **Connecting to Power:**
   - Ensure your design is connected to power using the power pins on the wrapper.
   - Use the `vccd1_connection` and `vssd1_connection` macros, which contain the necessary vias and nets for power connections.

2. **Flattening the Design:**
   - If you plan to flatten your design within the `openframe_project_wrapper`, do not buffer the analog pins using standard cells.

3. **Running Custom Steps:**
   - Execute the custom step in OpenLane that copies the power pins from the template DEF. If this step is skipped, the precheck will fail, and your design will not be powered.
