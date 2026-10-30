# Hardware and test context

| Item | Recorded context |
| --- | --- |
| Actuator | Gen 1 Bosch iBooster, Tesla Model X donor 1045523-00-C |
| Intended vehicle role | Potential hydraulic clutch actuator |
| Host | Windows |
| CAN adapter | DSD TECH SH-C30A |
| Tools | Cangaroo for capture; custom Python bench application for controlled experiments |
| Power | 12 V bench supply; project notes record a 10 A supply |
| Hydraulic context | Owner reported the assembly secured and master filled/bench-bled; final clutch circuit not demonstrated |
| Travel reference | Manually observed feedback span: 320 to 2961 raw counts |
| Independent sensors | The software does not acquire supply voltage/current, pressure, temperature, or independent output displacement |

A limited bench supply can confound interpretation of motion. The summarized runs do not establish behavior under the final clutch load or vehicle power conditions.

## Information to add with future tests

Record the physical label/photo, wiring diagram revision, actual supply behavior, hydraulic load, software commit, configuration, and associated captures. Distinguish the donor connector variant from any community pinout.

The owner roughly estimated **14–17 mm output movement at the 40% target** in the October 30 discussion. This is an approximate observation, not a calibrated conversion for the feedback signal or a release-bearing stroke measurement.

## Evidence availability

The initial documents were summarized from working-project notes and prior discussions. Raw logs and code are not included here yet. Add curated evidence before treating these summaries as independently reproducible test reports.
