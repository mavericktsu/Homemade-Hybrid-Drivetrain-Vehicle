# System map

The car is one project, but each subsystem has enough decisions and tradeoffs to deserve its own place. I've grouped them by what they do rather than the order in which I happened to work on them.

**Written up** means there's useful material to read. It does **not** mean the hardware is installed or validated.

## Powertrain and drivetrain

| Area | Status | Notes |
| --- | --- | --- |
| SBC 350 engine, intake, and fueling | Design in progress | Build choices, balance, damper, fuel system, and engine controls |
| Engine-disconnect clutch and hydraulics | Design in progress | Clutch selection, release bearing, disengagement, and control |
| [Bosch Gen 1 iBooster](ibooster-gen1/README.md) | **Bench investigation published** | CAN control, travel limits, response, faults, and recovery; being explored as a clutch actuator |
| AXM2 / G101A packaging and adapters | Design in progress | Shaft engagement, concentricity, supports, and keeping axial length under control |
| G-Force G101A transmission and shifting | Design in progress | Dog-box gearing, shifter, unloading, and speed matching |

## Hybrid electrical and controls

| Area | Status | Notes |
| --- | --- | --- |
| [AXM2 rotor-position sensing](motor-position-encoder/README.md) | **Design investigation published** | Resolver alternatives, inverter constraints, custom Hall encoder, and planned magnetic tests |
| DTI HV-500LC inverter and motor commissioning | Design in progress | Operating envelope, resolver/Hall interfaces, torque commands, and faults |
| Traction battery, BMS, and charging | Design in progress | Pacifica module count, protection, packaging, and charging |
| DC-DC, 12 V supply, and power distribution | Design in progress | DC-DC architecture, main disconnect, PDM, and electrical loads |
| ECUMaster ECU and hybrid control strategy | Design in progress | Engine control, torque arbitration, regen, shift assistance, and vehicle modes |
| Wiring and CAN networks | Design in progress | Harnesses, grounding, signal integrity, and diagnostic access |

## Chassis and driver systems

| Area | Status | Notes |
| --- | --- | --- |
| Factory Five chassis and packaging | Design in progress | Tunnel clearances and component placement |
| Cooling | Design in progress | Separate engine, motor/inverter, and battery circuits |
| Brakes, suspension, and tires | Design in progress | Hardware selection, track requirements, and interaction with regen |
| Driver controls, displays, and cockpit | Design in progress | ADU5, switches, custom displays, and driver feedback |
| Testing and vehicle validation | Not yet documented | Moving from bench results to a system that actually works in a car |

I plan to add separate sections as each of these becomes worth reading. The [project baseline](../docs/project-baseline.md) covers current choices in the meantime.

## How the detailed notes work

Each documented subsystem begins with an overview of the problem, the chosen direction, and what remains open. Under that I keep test plans, hardware details, and **dated experiments** where those are useful.

For example, the [iBooster overview](ibooster-gen1/README.md) is the readable entry point. The [experiment notebook](ibooster-gen1/experiments/README.md) preserves the actual sequence of bench work, including things that didn't work. I haven't moved those records around just to make the directory tree prettier; existing links to them should keep working.

For the motor-position project, start with the [selection story](motor-position-encoder/README.md), then the [planned linear magnet test](motor-position-encoder/test-plan.md).

[Back to project overview](../README.md)
