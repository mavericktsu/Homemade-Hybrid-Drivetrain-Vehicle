# October 30 — What happens when 0x38C goes missing?

## The problem

For a clutch actuator I need to know more than whether a normal position command works. If my controller pauses or CAN messages stop arriving, will the booster resume accepting requests when they return? My first suite deliberately interrupted messages to explore that.

Some interruptions left the unit reporting fresh position and candidate mode 7, yet ignoring external movement. That was different from losing the USB interface or simply waiting for an endpoint.

## What I tried

I omitted just **0x38C** while continuing the companion messages and receiving feedback. The historical suite called the 50 ms omission case 10, 100 ms case 11, then 250/500/1000 ms cases 12–14. I also stopped all application TX while keeping RX active and tried a stale neutral 0x38C stream for 100 ms at rest (case 20).

These were software interruptions, not a disconnected CAN cable. The adapter could still acknowledge received traffic. The rolling-counter behavior across the gap also mattered, so elapsed time wasn't the only variable.

Initially I judged recovery by released position and a ready-looking state. I added a small **0 → 5 → 0% re-arm probe** after discovering that the next motion could fail despite those status values. I then tried bounded disabled-request resynchronization, CAN-channel restart, and reopening the USB session. Some captured recovery sequences exhausted all four software methods without restoring motion.

## What I found

| Test condition | What happened on my bench |
| --- | --- |
| Nominal 50 ms 0x38C omission | Usually recovered; the host-queued gap was about 60 ms |
| Nominal 100 ms omission | Frequently lost external control; recorded queued gaps were around 110–120 ms |
| Longer omissions / stale request | Could leave external control unavailable after fresh traffic resumed |
| Fresh feedback and mode 7 after the event | Didn't reliably prove motion acceptance |
| Software reconnect / new handshake | Didn't consistently clear the condition |

This supports a freshness-sensitive interface near the 100 ms region in these tests. It doesn't establish an exact Bosch timeout: host queue timing isn't an independent bus timestamp, counters changed across the gaps, and I didn't sweep a tightly controlled threshold.

## Finding a practical reboot path

I first removed ignition power but left the permanent feeds connected. After roughly a minute the unit no longer gave manual assist. Reapplying ignition and restarting the handshake restored external control in my observation. USB stayed plugged in throughout.

I then deliberately faulted it with **historical case 36** and tried a shorter reset: I removed the **pin 17, 5 A fused +12 V feed for about 1–2 seconds**, leaving pin 1 motor power, pin 20 ignition, and USB connected. The booster appeared to reboot and accepted a new handshake.

That's my useful recovery finding. It is an operator-observed reboot and accepted handshake, not an archived post-reset 5% response trace proving recovery from every fault. It also supersedes my earlier suspicion that unplugging USB was always the decisive action.

## Separating this from PC and USB problems

I also encountered `Access denied` and `GsUsbBus was not properly shut down`. Those point to interface ownership/cleanup on the PC and can justify closing other CAN software, releasing handles, or reconnecting USB. They don't explain every event where the booster continued sending fresh feedback but ignored motion.

Likewise, adapter echoes aren't automatically replayed commands. I haven't found evidence that an “echo loop” caused the external-control inhibition. Link errors, host access errors, and fresh-feedback/no-motion behavior need different diagnoses.

## What I changed

Later software performs bounded recovery after a soft abort: wait for a fresh released state, attempt reconnection, and verify a small response before continuing Auto. The endpoint experiments later completed thirteen such post-probe response checks. That demonstrates cleanup after those endpoint probes, **not software clearance of the latched command-loss fault**.

I removed historical cases **10–20 and 36** from normal use because I had learned enough from them and they could require a power cycle. Hard CAN/driver errors still stop the sender. Automatic software recovery doesn't pretend it can switch a power feed that isn't connected to a controllable output.

For the vehicle I intend to supply the electronics feed through a switchable PDM channel. I still need an explicit clutch/drivetrain response while control is unavailable, reboot timing, and loaded verification. I haven't built that vehicle recovery system yet.

Recorded software-recovery sources include `test_suite_20261030_080721_997902` and `test_suite_20261030_082748_134430`; the ignition and pin-17 experiments are my observations from the session. The full captures remain in my working records.
