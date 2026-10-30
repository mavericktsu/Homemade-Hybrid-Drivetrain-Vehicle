# Gen 1 Bosch iBooster as a clutch actuator

I'm investigating whether a Tesla Gen 1 iBooster can operate my engine-disconnect clutch. I mainly need engage/disengage operation rather than sustained controlled slip. The attraction is a packaged electric actuator with position feedback and a CAN interface; the important question is whether its available stroke, speed, and fault behavior suit my clutch.

My unit is **P1045523-00C**. I've demonstrated remote movement on an essentially unloaded bench assembly. I haven't installed a clutch circuit or established loaded force, displaced volume, thermal endurance, or vehicle behavior.

## What I know so far

| Question | What I've found |
| --- | --- |
| Can I command movement? | Yes, on the private/Yaw CAN bus at 500 kbit/s. My helper closes a position loop around a native flow-like request. |
| How much travel is available? | Manual feedback covered 320–2961 raw. Remote motion repeatedly stopped at 1579 raw, **47.67% of that manual span**, across 13 endpoint probes. Physical output motion stopped too. |
| What working range am I using? | I've chosen a **45% target ceiling** to stay below that observed endpoint. The newer 45% profiles and ladders are prepared and checked offline; they aren't a completed loaded validation. |
| How fast does it move? | In measured 0–40% applies, 10–90% transit was about **0.20 s** at gains 20, 32, and 48. Gain 48 hunted instead of settling. More gain didn't improve that measured transit. |
| Can it cycle quickly? | Some scheduled cycles miss their endpoints. Aggressive full-range returns caused trouble, and a 2 Hz reversal test repeatedly ended in CAN ACK errors and apparent reboot behavior. I haven't established a maximum sustained full-stroke frequency. |
| What happens if commands go missing? | Omitting 0x38C around the 100 ms test duration often left external control unavailable even after fresh messages resumed. A 50 ms omission usually recovered. Timing/counter effects mean this isn't an exact OEM timeout measurement. |
| Can I recover without unplugging everything? | I restored handshake acceptance by briefly cycling the **pin 17, 5 A fused feed** while leaving motor power, ignition, and USB connected. Software recovery alone didn't consistently clear the latched condition. |
| Why did startup start cycling repeatedly? | It also happened without my helper transmitting, and I heard the PSU clicking during it. I currently suspect power delivery, but haven't confirmed the cause with a waveform or alternate supply. |

## A route through these notes

Start with [hardware and wiring](hardware.md), then the [bench workflow](bench-workflow.md) and [protocol explanation](protocol.md). The workflow explains what I mean by a handshake and why fresh “ready” feedback alone isn't proof that external motion is accepted.

The [experiment notebook](experiments/README.md) follows the work from first startup through command-loss recovery, endpoint characterization, speed testing, and the suspected power issue. [Lessons learned](lessons-learned.md) is the shorter version. I've included [curated evidence](data/README.md) and a [reference list](references.md) so the numbers and outside research have a trail.

## What this means for the vehicle

The consistent endpoint gives me a useful envelope to design around. It doesn't tell me whether the selected master/slave combination will release the clutch. The “percent” used here is a feedback normalization, **not millimetres or percent clutch disengagement**. My rough output measurement was only about 14–17 mm at 40%.

My current plan is to make the electronics feed switchable through the eventual PDM, so the vehicle can request a reboot if external control is lost. I still need to define what the drivetrain does while that happens. A recovered handshake is one part of that strategy; it doesn't by itself establish a safe clutch state.

Updated October 30, 2026. The working helper and complete raw logs remain outside this repository. Test IDs and helper versions in the notebook describe my local software, not Bosch diagnostic identifiers.
