# October 30 — Rapid-reversal failure, then a clicking power supply

## The problem

The **0–40%, 2 Hz reversal test** started moving, then aborted with a CAN ACK error. I heard internal motion that resembled a reboot/startup sequence and had to restart the handshake. Later, even an ordinary startup with no helper transmitting involved prolonged internal movement. I wanted to understand whether I was looking at another command fault or a power problem.

## What I checked in the reversal logs

Case 29 used gain 12 and a ±5120 flow cap in both directions, with 250 ms high and 250 ms low. It reversed the target even when the previous endpoint hadn't been reached. Across three captures, the first active-test ACK error occurred about **35.5–35.7 ms after a return-to-apply edge**.

| Saved session | Separation from final return-to-apply edge to ACK error |
| --- | ---: |
| `test_suite_20261030_122348_965443` | 35.740 ms |
| `test_suite_20261030_122809_713357` | 35.543 ms |
| `test_suite_20261030_122913_998820` | 35.532 ms |

The last measured position was around 14% while the new target was 40%, and the last queued request was +5120. Fresh feedback still showed candidate mode 7 about 13–15 ms before the ACK error. In the isolated runs, two endpoint legs had already been missed.

The old UI said “during CAN USB read” because that was the active receive operation. The underlying event was a **decoded CAN-controller ACK error**, not evidence of a USB permissions failure. I corrected the label in v0.9.3 and retired case 29 while keeping the stop on ACK error. The capture ends there, so it doesn't prove what the booster did afterward or why it stopped acknowledging.

## What I observed about the supply

My source is a **DPS3010U bench PSU**, not a battery. During the rapid test I saw its display move between about **12.5 and 13 V**. Earlier low displayed current had made me think power probably wasn't a concern, but those slow display readings don't record the fast minimum/maximum voltage at the connector.

Startup then became suspicious on its own. With the helper closed and no application CAN messages being sent, I heard internals moving back and forth for **10–15 seconds**, without obvious input/output shaft travel. Later it continued for minutes before eventually stopping. I noticed **clicking in the PSU while this was happening**.

This is the observation that most changed my view. The helper isn't required to trigger the cycling, and the PSU is doing something audible at the same time. I now suspect power delivery more than a newly discovered command sequence.

## What I think it means—and what remains open

The [DPS-series documentation](https://www.wanptek.com/productinfo/1171926.html) describes current limiting and over-current protection with output shutdown/recovery. Protection or regulation interacting with startup demand could plausibly create a repeated-start cycle. Clicking alone doesn't prove that protection tripped, nor exclude wiring resistance, excessive load, or a unit fault.

The [DPS manual](https://device.report/m/fb615a27c49f7ea4f21c3143724ed9910adf86004df6302ee68ae5d9479e1eb9_optim.pdf) explains the difference between constant-current limiting and over-current shutdown. I haven't recorded the actual current-limit setting or a synchronized CC/OCP indication during the event. The PSU's 10 A rating and its displayed average current don't answer those questions.

Bosch's [general iBooster summary](https://www.bosch-mobility.com/media/global/solutions/passenger-cars-and-light-commercial-vehicles/driving-safety-systems/brake-booster/ibooster/summary-ibooster.pdf) lists a main operating range of 9.8–16 V. That isn't a reset-threshold specification for this exact part, and my 12.5–13 V display reading doesn't rule out a much shorter dip or rise at the booster. I also haven't established whether this PSU can absorb returned motor energy; I haven't measured returned energy here.

## Where I've left it

I'm generally happy with the useful travel, control, and unloaded speed characterization, and I believe the supply is the likely next issue to resolve. I **haven't confirmed a fix**, obtained a fast voltage waveform, or compared with an alternate suitable source; I don't currently have a battery supply available.

The most informative next measurement would capture voltage at **pin 1 and pin 17 relative to pin 9** through startup or a reversal, along with the protection state/current. Until then I keep “suspected PSU issue” separate from the observed ACK loss and apparent reboot. I haven't changed calibration routines, cleared diagnostic faults, or treated the prolonged cycling as normal behavior.
