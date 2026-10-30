# October 30 — Speed, release behavior, and repeated movement

## The problem

For clutch operation I care about repeatable movement, especially a fast disengagement followed by an acceptable engagement. A single quick apply isn't enough. My early timed-cycle tests also made it easy to confuse a fast-changing target with a fast actuator.

## What I tried and learned

The original duty tests scheduled 0–40% targets at 0.5, 1, and 2 Hz, with different high-position dwell fractions. Some completed while missing endpoints. In a later full run, historical cases 25–28 completed with tracking-limit results, then the **2 Hz, 50%-high, five-cycle test (case 29)** stopped on a CAN ACK error. I describe that separate failure in the [power-supply investigation](2026-10-30-startup-power-supply.md).

At 2 Hz each half-cycle lasts only 250 ms. The target changes on schedule even if the assembly is still moving toward the previous endpoint. Completion of those target changes doesn't demonstrate full-stroke 2 Hz operation, and 50% command dwell isn't a motor thermal-duty measurement.

I also compared return profiles. One **0–40% sequence with gain-12 apply, gain-2 return capped at ±2048, one second high and three seconds low**, completed three cycles without endpoint misses (historical case 37). An inner-band **15–40%, 0.5 Hz** comparison also worked (case 34). A full-range comparison with a more aggressive gain-12 return failed into candidate mode 4 around 12–13% travel (case 35).

That makes release profile and operating range useful variables. It doesn't establish one universal maximum release speed or tell me whether the cause was control interaction, hydraulic/mechanical behavior, or power delivery. Separately, gains 20 and 32 gave about 0.20 s 10–90% apply transit in the [step comparison](2026-10-30-travel-and-gain.md), while gain 48 hunted.

## The newer 45% profiles

After the endpoint work I extended the prepared normal profiles to **45%**, below the observed 47.67% ceiling. Historical cases 79–91 compare apply gains 12/20/32 and several gentler return settings, including gain-2 return caps from 1024 to 5120 and return gains 4/6/8/12. They use a 47% protective cutoff. I haven't recorded a completed hardware validation of this new group here.

I also prepared two endpoint-driven reversing ladders, historical cases 92 and 93. The requested path is:

```text
up:   0, 5, 0, 10, 5, 15, 10, 20, 15, 25, 20, 30, 25, 35, 30, 40, 35, 45
down: 40, 45, 35, 40, 30, 35, 25, 30, 20, 25, 15, 20, 10, 15, 5, 10, 0
```

There are 34 moves. Instead of reversing on a fixed frequency, each move waits for 50 ms within ±8 raw counts of its endpoint, with a five-second move deadline and a two-second no-progress guard. Apply uses gain 20 / cap 5120; return uses gain 4 or 8 / cap 2048. There are no extra holds between moves. This should let me observe rapid changes without demanding an impossible timetable, but it remains a prepared test, not a reported successful hardware run.

## Keeping the suite useful

I added Auto and single-test operation so I can repeat a selected case while watching different aspects. I retained old case numbers for matching logs, but retired known troublesome experiments from the runnable list: **10–20, 29, 36, 39–53, and 56**. In v0.9.3 that leaves 64 runnable cases among 93 historical IDs.

My latest offline validation reports 104 test methods covering software behavior with mocks and replay. Those aren't 104 hardware experiments. In particular, prepared 45% ladders, software cleanup, and the oscillation guard still need to be distinguished from measured motion results.

I consider the unloaded response characterization useful now. Before pushing sustained speed harder, I need to resolve the power-supply question and eventually measure motion under the actual clutch load. Low current on this unloaded bench is encouraging, but it doesn't establish electrical peaks or endurance.
