# My project baseline

Updated October 30, 2026. This is where I keep the current direction when older notes describe a different plan.

## Choices I've made

| Topic | My current direction | What it supersedes |
| --- | --- | --- |
| ECU / hybrid supervisor | Ecumaster EMU Pro 16 | Earlier Holley Terminator X architecture |
| Battery and motor winding | At least seven LG Chem Pacifica modules, AXM2 medium winding | Six-module electrical target |
| Battery packaging | Revised packaging remains open | The six-module 2x2 lower plus two upper CAD study doesn't establish a seven-module fit |
| Gearbox | Conventional G101A dog box with NASCAR gear set | Assumptions about a dedicated drag-racing clutchless transmission |
| Shift strategy | Motor-led unloading and speed matching, minimal SBC intervention | Assuming routine engine cuts |
| Clutch actuator | Gen 1 iBooster bench investigation | Earlier actuator choices or an assumption that the clutch installation is complete |

My gearbox configuration is **2.60 / 1.90 / 1.38 / 1.00**, recorded October 20, 2026. The broader component list is in the [project overview](../README.md).

## Performance intent

I'm planning around approximately **425 crank hp** and a **7,000 rpm design ceiling** for the engine. Neither is a measured result or an approved operating limit. Electric launch, low-speed EV operation, hybrid transitions, assistance, regeneration, and motor-assisted shifts are objectives whose usable envelopes still need to be demonstrated.

I won't add a motor peak rating to an engine target and present the sum as installed hybrid output. I need compatible speed points, actual electrical limits, and substantiated system behavior first. Vehicle weight, acceleration, top speed, electric range, sustained output, and usable battery energy remain open.

## Questions I'm still working through

- What engine build, heads/cam, fuel/ignition hardware, and operating limit will I settle on?
- Which clutch, release bearing, hydraulics, shaft supports, and P2 interfaces will work together?
- Will the iBooster's available remote stroke and displaced volume release that clutch under load?
- What final battery count, module revision, enclosure, and installation will I use?
- Which inverter/BMS variants, firmware, and interfaces will be commissioned?
- How will charging, power distribution, torque arbitration, and fault recovery behave at vehicle level?
- What will packaging, cooling, and measured performance actually allow?

The iBooster now has a useful [bench characterization](../systems/ibooster-gen1/README.md). Its remote endpoint, recovery behavior, and unloaded response measurements help narrow the design, but don't answer the clutch-load question yet.

## Where these notes come from

I assembled the initial baseline from my component register, engine worksheet, dashboard notes, virtual-dyno notes, battery handoffs, gearbox discussions, and bench records. Those working documents aren't all public. Where my older records disagree, I use my later explicit decisions; simulator defaults remain assumptions. I'll replace these summaries with linked measurements and drawings as I publish them.
