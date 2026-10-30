# What I'd tell someone starting this experiment

The basic CAN control worked, but the useful lessons came from separating things that looked alike on the screen.

| What tripped me up | What I learned | Detail |
| --- | --- | --- |
| Touching ignition briefly seemed to wake everything | Maintain ignition for the session; delayed shutdown can disguise a missing wake input | [Startup](experiments/2026-10-29-startup.md) |
| “Test completed” looked like proof of movement | Check feedback response and physical output; a tiny step or scheduled cycle can finish without achieving the intended motion | [Workflow](bench-workflow.md) |
| Mode 7 looked like recovered control | Use a small functional response check; fresh status can coexist with ignored external requests | [Recovery](experiments/2026-10-30-command-loss-and-recovery.md) |
| I initially suspected USB or CAN echoes for every failure | Separate device access errors, link-level errors, and external-control inhibition | [Recovery](experiments/2026-10-30-command-loss-and-recovery.md) |
| Resuming fresh 0x38C didn't always restore motion | Treat request freshness/counters seriously; I found a pin-17 reboot path when software recovery wasn't enough | [Recovery](experiments/2026-10-30-command-loss-and-recovery.md) |
| Percent travel sounded like a mechanical specification | My scale is 320–2961 manual feedback, not millimetres, fluid volume, or clutch disengagement | [Hardware](hardware.md) |
| Higher target or gain might push past the remote endpoint | Direct-flow and limit-field variations still stopped at 1579 raw; no verified override emerged | [Endpoint probes](experiments/2026-10-30-endpoint-characterization.md) |
| More gain seemed like the route to more speed | Gain 48 hunted without improving measured transit over 20/32 | [Gain comparison](experiments/2026-10-30-travel-and-gain.md) |
| A 2 Hz target schedule sounded like 2 Hz full-stroke motion | Check endpoint misses; unfinished reversals are a different test from completed strokes | [Speed and duty](experiments/2026-10-30-speed-duty-and-ladder.md) |
| Low displayed amps made the supply seem adequate | My PSU later clicked during standalone startup cycling; average display readings don't establish transient behavior | [Power investigation](experiments/2026-10-30-startup-power-supply.md) |

My present working choices are a 45% target ceiling, moderate apply gain, gentler return profiles, actual-response verification after recovery, and eventual switchable electronics power. These are development choices from one unloaded unit. The next major step is resolving power delivery and checking whether the selected clutch hydraulics fit the measured envelope.
