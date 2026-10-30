# Remote travel ceiling and gain comparison

Date: October 30, 2026. Evidence type: summary of bench logs, development notes, and owner observations.

## Questions

How much of the manually observed feedback span is remotely available, and does higher host position-loop gain improve the response?

## Reference and setup

The working project uses 320 raw as released and 2961 raw as the manual full-span reference. Percentages below refer to that signal span. They do not mean calibrated stroke or clutch engagement.

Testing used the identified donor unit without a demonstrated final clutch load. Software notes identify the v0.8-series suite; exact code commits and raw captures are not published here.

## Travel observations

The recorded session `test_suite_20261030_092726_743733` contained one completed 40% case, eight 60% attempts, and seven intervening successful 5% recovery checks.

Each 60% attempt plateaued at **1579 raw, approximately 47.67%** of the manual span, below its 1905-raw target. Requests and feedback continued, and reference mode remained 7. The owner later reported that physical output motion also stopped.

The owner roughly estimated **14–17 mm output travel at the 40% target**. That estimate is not a measured conversion curve, and no linear extrapolation is adopted here.

## Gain comparison

The development note summarizes the 09:37 and 09:38 sessions:

| Apply gain | Observed 10–90% transit | Settling result |
| --- | --- | --- |
| 20 | About 0.20 s | Settled; about 0.57 s including required dwell |
| 32 | About 0.20 s | Settled; about 0.67 s including required dwell |
| 48, two attempts | About 0.20 s | Did not settle within the ten-second deadline |

The gain-48 runs repeatedly crossed the target at approximately 7.1 cycles/s while reference mode and feedback remained normal. This is consistent with unstable host-loop behavior on this setup, without isolating every hydraulic or mechanical contribution.

## Conclusions and limits

The observed remote endpoint is repeatable in these runs; its cause remains unresolved. It is not established as a universal Bosch limit or a documented firmware setting.

Higher gain did not improve the compared transit times and worsened settling. Gain 20 was the best baseline among these particular runs, not a commissioned calibration.

A sustained-oscillation guard was subsequently checked against recorded traces offline. That does not demonstrate its live physical stopping behavior.

## Next investigation

Prepared v0.9 endpoint cases compare selected external-limit fields and direct-flow versus position-loop requests. Automatic software recovery has also been prepared. No successful outcome from those new hardware experiments is claimed here.

Add raw captures, exact software/configuration revisions, independent displacement and hydraulic-load measurements, and recovery outcomes to future records.
