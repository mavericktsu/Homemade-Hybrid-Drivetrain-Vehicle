# Project baseline

This is the short version of what I've settled on, what I'm still considering, and what I haven't proved. I change things fairly often as the packaging and hardware become clearer, so older experiments may describe a different plan.

**Last reviewed: November 18, 2026.** The choices below are design decisions unless explicitly identified as bench results.

## Drivetrain

| Area | Current direction | Still to work out |
| --- | --- | --- |
| Chassis | Factory Five Gen 3 Type 65 Coupe | Packaging and actual fitment |
| Engine | Naturally aspirated Gen I SBC 350, approximately 425 crank hp initial goal | Final engine build, power curve, and operating limit |
| Engine disconnect | Conventional clutch between SBC and AXM2 | Bellhousing interfaces, release geometry, hydraulic actuation |
| Electric motor | Beyond Motors AXM2, medium-voltage winding | Mounting, shaft coupling, cooling, commissioning |
| Transmission | G-Force G101A, ratios **2.60 / 1.90 / 1.38 / 1.00** | Shifter linkage, shift sensing, integration |
| Shift strategy | Use motor torque for dog unloading and speed matching; minimize routine V8 intervention | Real shift performance and fault handling |
| Clutch actuator | Tesla Gen 1 Bosch iBooster under investigation | Whether its travel/force and reliability are sufficient under clutch load |

I originally spent time looking at other drivetrain and transmission arrangements. The P2 layout is the direction I've kept because I want electric-only operation **and** a real disconnect between the V8 and the motor. The penalty is the amount of hardware that now has to fit between the engine and gearbox.

## High voltage and controls

| Area | Current direction | Still to work out |
| --- | --- | --- |
| Battery | At least seven LG Chem Pacifica 16S modules | Final number, voltage envelope, packaging, BMS and charging |
| Inverter | DTI HV-500LC | Final control configuration, limits, safety behavior, and commissioning |
| Motor position | Custom 20-pole ring and three Hall latches | DTI Hall compatibility, pole spacing, retention, and full-speed validation |
| ECU / hybrid supervisor | Ecumaster EMU Pro 16 | Torque arbitration, traction strategy, regen, shift integration |
| Power distribution | Hardwire Electronics PDM28 selected; 12 V supply architecture still evolving | Protection, disconnect behavior, loads, wiring |
| Display | Ecumaster ADU5; additional driver displays under design | Integration and final cockpit layout |

The Hall encoder is a **chosen development direction**, not a working substitute for a resolver yet. The [position-sensing notes](../systems/motor-position-encoder/README.md) explain how I got there.

## Performance targets

I'm working around approximately **425 crank hp** for the initial engine and using **7,000 RPM as a design ceiling** during engine development. I'm interested in electric launch/low-speed operation, torque assistance, regeneration, and motor-assisted shifting.

Those are goals, not measured vehicle capabilities. I haven't established combined output, total vehicle weight, sustained hybrid performance, range, acceleration, or an approved engine speed limit. Adding the motor's peak rating to the engine's peak rating would make a nice number, but it wouldn't tell me what the drivetrain can actually deliver at one speed point.

## What I've actually tested

**iBooster:** I've commanded the Tesla Gen 1 unit on an unloaded bench setup, measured a consistent remote-travel ceiling, looked at its response and command-loss behavior, and found a usable power-cycle recovery path. I have **not** established loaded clutch operation. Details and recorded limitations are in the [iBooster section](../systems/ibooster-gen1/README.md).

**Motor position:** I've researched and designed around several resolver arrangements, laid out an initial Hall PCB, and developed a plan to measure the magnetic geometry. I have **not** run the custom Hall encoder with the DTI inverter. See [motor-position sensing](../systems/motor-position-encoder/README.md).

## What comes next

The most consequential open work is getting the motor/gearbox/engine interfaces physically right, validating motor-position feedback, sorting the full electrical safety and power architecture, and then commissioning the pieces together. Small component choices are still moving around within that larger picture.

The [system map](../systems/README.md) is the best place to find individual topics as I document them. Older notes are useful for understanding why a choice changed, but this page takes precedence for the current direction.
