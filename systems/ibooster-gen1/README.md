# Trying to use a Tesla iBooster to operate the clutch

The engine-disconnect clutch needs an actuator, and I wanted to avoid adding a conventional hydraulic pedal setup to a car where the clutch is mostly going to be controlled electronically anyway. That led me to the Tesla Gen 1 Bosch iBooster. It's a compact electric actuator with CAN control and position feedback, which sounds almost suspiciously convenient.

I'm not trying to use it for precise, sustained clutch slip. Mostly I need reliable engagement and disengagement. The problem is figuring out how much usable travel it really has, how quickly it can move, and what it does when communication or power gets interrupted.

The unit I'm testing is **P1045523-00C**. I've gotten it moving on the bench, but that's with essentially no clutch load. I haven't connected it to the clutch hydraulics, and I don't yet know if it has enough usable output to operate the clutch reliably.

## What the bench testing has actually shown

| Question | What I've found |
| --- | --- |
| Can I command movement? | Yes, on the private/Yaw CAN bus at 500 kbit/s. My helper closes a position loop around a native flow-like request. |
| How much travel is available? | Manual feedback covered 320–2961 raw. Remote motion repeatedly stopped at 1579 raw, **47.67% of that manual span**, in the earlier endpoint probes and the later ordinary/pattern-2 comparison. Physical output motion stopped too. |
| What working range am I using? | I've chosen a **45% target ceiling** to stay below that observed endpoint. Later gentle diagnostic holds at 45% completed; that does not validate the prepared ladders or operation under clutch load. |
| How fast does it move? | In measured 0–40% applies, 10–90% transit was about **0.20 s** at gains 20, 32, and 48. Gain 48 hunted instead of settling. More gain didn't improve that measured transit. |
| Can it cycle quickly? | Some scheduled cycles miss their endpoints. Aggressive full-range returns caused trouble, and a 2 Hz reversal test repeatedly ended in CAN ACK errors and apparent reboot behavior. I haven't established a maximum sustained full-stroke frequency. |
| What happens if commands go missing? | Omitting 0x38C around the 100 ms test duration often left external control unavailable even after fresh messages resumed. A 50 ms omission usually recovered. Timing/counter effects mean this isn't an exact OEM timeout measurement. |
| Can I recover without unplugging everything? | I restored handshake acceptance by briefly cycling the **pin 17, 5 A fused feed** while leaving motor power, ignition, and USB connected. Software recovery alone didn't consistently clear the latched condition. |
| Why did startup start cycling repeatedly? | It also happened without my helper transmitting, and I heard the PSU clicking during it. I currently suspect power delivery, but haven't confirmed the cause with a waveform or alternate supply. |

## Chasing the travel limit

The big question was whether the ~48% ceiling was a limitation of my commands rather than the unit itself. I tried another request pattern near the limit, checked ECU identity and faults, and watched diagnostics during gentle movement. Both request patterns still plateaued at raw 1579. A runout candidate changed near 40.4%, but its physical meaning and causal role remain unknown. Changing one source-derived CAN ignition bit did not prevent the observed plausibility indication.

I also tried **82 ID/payload combinations across 41 IDs**, using two packet shapes and watching the feedback, diagnostics, and fault fields. None produced a detected change. Those stationary probes did not retest travel afterward and do not rule out a silent state change, another payload format or a message sequence.

The [dated testing summary](experiments/2026-11-05-remote-travel-and-message-discovery.md) explains the comparisons, interrupted attempts, software corrections and remaining blind spots. The [derived evidence](data/remote-control-investigation-summary.json) records coverage and capture hashes. I have no demonstrated full-travel mode to depend on in the build.

## The detailed notes

If you're interested in reproducing the setup, start with [hardware and wiring](hardware.md), then the [bench workflow](bench-workflow.md) and [protocol explanation](protocol.md). The workflow explains what I mean by a handshake and why fresh “ready” feedback alone isn't proof that external motion is accepted.

The [experiment notebook](experiments/README.md) follows the work from first startup through command-loss recovery, endpoint characterization, speed testing, and the suspected power issue. [Lessons learned](lessons-learned.md) is the shorter version. I've included [curated evidence](data/README.md) and a [reference list](references.md) so the numbers and outside research have a trail.

## So can it actually operate the clutch?

Possibly. The consistent endpoint gives me something real to design around, which is a lot better than guessing. But it doesn't tell me whether my master/slave combination will release the clutch. The “percent” used here is a feedback normalization, **not millimetres or percent clutch disengagement**. My rough output measurement was only about 14–17 mm at 40%.

My current plan is to make the electronics feed switchable through the PDM, so the car can power-cycle the unit if external control gets stuck. I still need to define what the drivetrain does while that happens. A recovered handshake is one part of that strategy; it doesn't by itself establish a safe clutch state.

Bench results through November 5, 2026. The working helper and complete raw logs remain outside this repository. Test IDs and helper versions in the notebook describe my local software, not Bosch diagnostic identifiers.

