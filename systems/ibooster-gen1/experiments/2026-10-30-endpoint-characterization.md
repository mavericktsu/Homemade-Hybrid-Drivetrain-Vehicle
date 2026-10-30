# October 30 — Is the remote-travel ceiling in my position loop?

## The problem

Remote output kept stopping around 47.7% even though manual input went farther. Before designing around it, I wanted to check whether my own position loop or request cap was creating the limit.

## What I tried

With helper v0.9, I ran thirteen endpoint probes: historical cases 67–78, including one repeat of 68. I compared three approaches:

- Position-loop commands with the reference external limit field set to 120, 80, 40, 20, 5, then 120 again.
- Fixed positive native requests of +256, +512, +1024, +2048, and +5120, bypassing the host position loop during the probe.
- A slow gain-2, cap-512 position-loop approach.

I logged outgoing host-queued frames to check that the intended fields actually changed. After each probe I withdrew the request and required a small **0 → 5 → 0% response check** before proceeding.

## What I found

**All thirteen probes reached exactly 1579 raw** in the recorded feedback. The position-loop cases still requested positive flow at the endpoint. Fixed request magnitude changed how long it took to get there, but didn't change the endpoint: about 6.310 seconds for +256 versus 1.259 seconds for +5120, including the 0.5-second plateau-detection interval.

Candidate mode stayed 7, the maximum feedback gap across the runs was about 12.6 ms, and all thirteen post-probe response checks succeeded. I independently observed physical output motion stopping at that endpoint.

The [endpoint summary CSV](../data/endpoint-summary.csv) preserves each run's settings and result. Its source is `test_suite_20261030_103505_731502.csv` and the companion report. The queued-message analysis isn't an independent on-wire capture, even though my local analysis filename included the word “wire.”

## What I think it means

The limit is downstream of my proportional position loop: direct native requests reached the same point. Raising gain or waiting longer wasn't supported as a way around it.

An internal external-control constraint or a motor-driven travel limitation remains plausible. I haven't identified a firmware constant, proven that all Gen 1 units have this same endpoint, or found a verified command that extends it. The unchanged limit-field tests don't prove that field is ignored: this was unloaded, I didn't measure pressure, and actual pressure may have stayed below every limit I tried.

## Where I stopped exploring

I chose to work below the repeatable endpoint rather than keep searching indefinitely for an override. My new normal target ceiling is **45% / 1508 raw**, with a **47% / 1561 raw cutoff** for those profiles. That is a working envelope based on this unit, not a validated margin under load. The next useful question is whether the stroke and hydraulic volume are enough for the selected clutch.
