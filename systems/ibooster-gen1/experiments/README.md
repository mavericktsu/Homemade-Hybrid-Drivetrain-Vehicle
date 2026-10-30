# My iBooster experiment notebook

I started by trying to make the unit wake reliably, then moved on to the questions that matter for clutch control: accepted commands, available travel, response speed, and what happens when communication goes wrong.

| Date | Problem and result |
| --- | --- |
| Oct 29 | [Startup and the first tiny step](2026-10-29-startup.md): maintaining ignition fixed an early communication loss; a completed tiny request wasn't convincing evidence of motion. |
| Oct 30 | [Command loss and recovery](2026-10-30-command-loss-and-recovery.md): short omissions could recover, longer ones could inhibit external control; cycling the electronics feed restored handshake acceptance. |
| Oct 30 | [Travel and gain](2026-10-30-travel-and-gain.md): manual feedback established a percent scale, remote travel plateaued, and gain 48 caused hunting. |
| Oct 30 | [Endpoint characterization](2026-10-30-endpoint-characterization.md): thirteen probes reached the same 1579-raw endpoint despite different request magnitudes and limit-field values. |
| Oct 30 | [Speed, duty cycle, and the ladder](2026-10-30-speed-duty-and-ladder.md): aggressive reversal and scheduled timing exposed different limits; I prepared a 45% range and endpoint-driven ladder. |
| Oct 30 | [Rapid-reversal failure and suspected PSU trouble](2026-10-30-startup-power-supply.md): repeated ACK loss near reversals, then prolonged standalone startup motion with PSU clicking. Power delivery is my current suspect, not a proven diagnosis. |

Several entries share a date because I explored them in parallel during one bench session. The order above is a reading order, not a claim that every test happened in that sequence. [Curated data](../data/README.md) identifies the specific saved sessions behind the main measurements.
