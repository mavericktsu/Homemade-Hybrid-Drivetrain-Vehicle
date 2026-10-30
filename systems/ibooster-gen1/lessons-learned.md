# Lessons learned

Updated October 30, 2026. These findings apply to the recorded bench setup.

| Lesson | What supports it | What remains open |
| --- | --- | --- |
| Startup and motion are separate milestones | Sustained readiness feedback followed maintained ignition; the initial small motion request was inconclusive | Loaded actuator operation |
| A feedback span is not a physical stroke calibration | The 0–100% reference came from manual operation | Output displacement and hydraulic volume versus feedback |
| Manual travel does not prove the same remote range | Repeated remote requests stopped near 47.7%; the owner observed physical motion stop | Cause and whether configuration changes affect it |
| More gain can worsen control | Gains 20/32/48 had similar transit times; gain 48 oscillated | Loaded tuning, repeatability, and actual control margin |
| Readiness alone does not prove useful control | Mode 7 and fresh feedback continued during endpoint plateau and oscillation | Verified response after each recovery |
| Software recovery differs from power cycling | The bench tool can reconnect/rebootstrap but has no attached power-control channel | Implemented and verified hardware recovery |
| Offline tests and prepared cases are separate from hardware results | The latest guard and endpoint experiments were developed in software | Live outcomes for those revisions |

Read the [startup record](experiments/2026-10-29-startup.md) and [travel/gain record](experiments/2026-10-30-travel-and-gain.md) for context.

The host uses feedback error to produce the community reference's native flow request. Its position slider does not establish an independently verified native absolute-position command. Control-field names and readiness values remain interpretations that need confirmation on the actual unit.
