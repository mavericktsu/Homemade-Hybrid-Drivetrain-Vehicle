# Project baseline

Updated October 30, 2026. This page records intended configuration and decisions recovered from the existing project records.

## Decisions that supersede older notes

| Topic | Current direction | Historical material to avoid treating as current |
| --- | --- | --- |
| ECU / VCU | Ecumaster EMU Pro 16 | Earlier Holley Terminator X architecture |
| Battery | At least seven LG Chem Pacifica modules, AXM2 medium winding | Six-module electrical target |
| Battery packaging | Revised packaging remains open | Existing six-module 2x2 lower plus two upper CAD study is not proof of a seven-module fit |
| Gearbox | Conventional G101A dog box with NASCAR gear set | Assuming a dedicated drag-racing clutchless transmission |
| Shift strategy | Motor-led unloading and speed matching, minimal SBC intervention | Assuming routine engine cuts |
| Clutch actuator | Investigating Gen 1 iBooster on the bench | Earlier actuator selections or a completed clutch installation |

The owner supplied gearbox ratios **2.60 / 1.90 / 1.38 / 1.00** on October 20, 2026.

## Performance intent

The engine target is approximately **425 crank hp** with a requested maximum **7,000 rpm** design ceiling. Neither is a measured result or an approved engine operating limit.

EV launch, low-speed EV operation, hybrid transition, propulsion assistance, regeneration, and motor-assisted shifts are control objectives. Their usable operating envelopes remain to be demonstrated.

Do not add a motor peak rating to the engine target and call the sum installed hybrid output. Combined performance requires compatible speed points, actual battery/inverter limits, and measured or substantiated system behavior.

Vehicle weight, acceleration times, top speed, electric range, sustained hybrid output, and usable battery energy remain unestablished here.

## Open decisions

- Final engine build, heads/cam, fuel/ignition hardware, and operating limit.
- Matched clutch, release bearing, hydraulics, shaft support, and P2 mechanical interfaces.
- Whether the iBooster's available remote stroke and displaced volume suit the selected clutch under load.
- Final battery count, module identification, enclosure, and installation.
- Exact inverter/BMS variants, firmware, and control interfaces.
- Charging, power distribution, protection, torque arbitration, and fault behavior.
- Vehicle-level packaging, cooling, and measured performance.

## Basis of this initial summary

The seed material is the existing Type 65 working project: its component register, engine base-map worksheet, auxiliary-dashboard notes, virtual-dyno model notes, battery-intent handoff, iBooster bench notes, and prior discussions about the gearbox and actuator tests.

Those working records are not yet published in this repository. This page is a curated summary, not an independently verified specification. Where records disagree, later explicit owner decisions take priority; simulator defaults remain assumptions.
