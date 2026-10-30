# How I run a bench session

My aim is to separate basic communication, external-control acceptance, and motion performance. Early on I treated a ready-looking status as proof I had control; later fault tests showed why that wasn't enough.

This page describes my method, alongside the [hardware setup](hardware.md) and [protocol](protocol.md). My custom helper isn't published here yet, so this isn't a download-and-run software package. The parameters and notebook explain what I tested; exact UI buttons refer to my local helper.

## Before starting

I secure the assembly, arrange the master-cylinder circuit for the intended bench test, keep clear of moving parts, and keep a physical power disconnect available. Software withdrawal depends on communications and doesn't guarantee a physical stop. These results are from an unloaded assembly, not a clutch or brake installation.

I check the two private-CAN wires, common reference, termination, and maintained ignition connection. I close Cangaroo and other helpers before letting one program claim the USB adapter. For a new setup, I first listen and confirm valid feedback at 500 kbit/s rather than starting with motion commands.

## Startup and handshake

1. I establish the permanent power feeds, then maintain ignition power. My helper can start its disabled-request traffic before ignition is applied, so it sees the startup transition.
2. During handshake it sends the reference message set with the external request **disabled** and the flow-like request neutral. I keep receiving feedback throughout.
3. I wait for fresh, CRC-valid position/status feedback and a stable readiness interval. Later versions require roughly one second of quiet, fresh candidate-mode-7 feedback before allowing a test.
4. I use a small **0 → 5 → 0% functional check** when I need to verify regained control. A status value alone isn't the check: actual feedback must respond and return.

There is no explicit “run calibration” command in my helper. The internal movement I hear on startup can happen with the helper closed. I also don't use the time from first received frame to readiness as a calibration measurement: my older logs don't mark the ignition-switch instant.

## Motion tests and recording

I start with released baseline and a small checkout, then a single apply/hold/return. I log target, measured raw position, native request, request-enable state, candidate mode, frame timing, errors, and the test phase. I preserve the failed run before changing settings.

My helper offers **Auto** to continue through the selected and following cases, or **single-test mode** to run and repeat one case. Repeating a case is useful for watching a different detail, but a completed software case doesn't automatically mean the requested mechanical endpoints were reached. I check endpoint misses, settling, and tracking results separately.

For position steps I distinguish:

- **10–90% transit:** time between crossing 10% and 90% of the requested move, not the entire manual travel range.
- **Settled time:** entry into the configured target band plus its required dwell.
- **Overshoot:** feedback beyond the target, in raw counts.
- **Cycle tracking:** whether each requested endpoint was actually reached before the next scheduled reversal.

For duty-cycle tests, “50% high” means the target spends half its period at the high position. It doesn't measure motor electrical duty, current, or thermal loading.

## Stopping and recovering

At a normal end I withdraw the external request and continue checking feedback. If transmission or reception fails, the last commanded state and the actual state can differ; the display marks feedback stale rather than claiming release.

For a soft abort, later software can wait for a fresh released state, attempt bounded reconnection, and perform the small response check before Auto continues to the next case. It doesn't simply replay the failed target. Hard CAN/driver/timing or envelope failures stop the sender. The detailed distinction is in [command-loss and recovery](experiments/2026-10-30-command-loss-and-recovery.md).

If the helper reports USB access denied, I stop other CAN applications and release their device handles; reconnecting USB helped earlier host-access problems. If feedback remains fresh but external motion is ignored after a command-loss test, I treat that as a different problem. The pin-17 power-cycle observation is documented in the recovery entry, with its verification limits.

I have retired the deliberate communication-fault tests and the problematic 2 Hz reversal from the normal selector. I don't need to repeatedly provoke known unrecovered failures to measure motion speed.
