# October 30 — Finding a travel scale and a useful position gain

## The problem

Once larger commands worked, I needed a usable travel range and a way to compare response speed. Raw feedback alone didn't tell me how much movement I was asking for, and the first tiny step was too small to judge by eye.

## What I tried

I recorded several manual rod sweeps in `travel.asc`, including travel near the far end. That gave me rest at **320 raw** and an observed upper end of **2961 raw**. I normalized that span to 0–100%; the end bounced somewhat, so I didn't treat it as a surveyed stroke calibration.

I used that scale for a host-side proportional position loop and compared 0–40% applies with gains 20, 32, and 48, all with a flow cap of ±5120. I also tried targets above 40% to see whether the available remote range matched the manual range.

## What I found about travel

A 40% target is **1376 raw** and was reachable. Eight attempts at a 60% target in one earlier session stopped around **1579 raw**, approximately **47.67%** of my manual span. I could manually move farther. I also confirmed that the physical output stopped at the remote plateau, rather than just the displayed position freezing.

My rough output measurement at 40% was **14–17 mm**. It was difficult to measure and wasn't instrumented. I haven't used it to infer a full-stroke length or the final clutch's required displacement. The [endpoint follow-up](2026-10-30-endpoint-characterization.md) explores the plateau more systematically.

## What I found about gain

| Host gain | 10–90% transit | Settled time | Overshoot | Result |
| --- | ---: | ---: | ---: | --- |
| 20 | 0.2000 s | 0.5691 s | 19 raw | Settled |
| 32 | 0.2000 s | 0.6693 s | 45 raw | Settled |
| 48 | 0.2000 s | Not observed before deadline | 65 raw | Sustained hunting |
| 48, repeat | 0.2006 s | Not observed before deadline | 74 raw | Sustained hunting again |

Settled time includes the required 200 ms dwell in the target band. The gain-48 runs continued receiving fresh feedback in candidate mode 7, but oscillated near the target until their roughly ten-second deadlines. I saw the input shaft bouncing too. Feedback crossings suggested about **7.1 Hz** hunting.

More gain hadn't bought me a faster measured transit. It had increased overshoot and made the loop unstable. Gain 20 was the best observed baseline in this small comparison; gain 32 also worked. I haven't established an exact stability boundary between 32 and 48, or a universal hardware speed limit.

## What I changed

I retired gain 48 from normal motion tests and added a fixed-target oscillation guard. Offline replay detects both recorded hunting traces early, without rejecting the lower-gain traces or synthetic in-band noise. That checks the detection logic; it isn't a hardware demonstration of the new stop response.

The saved sessions are `test_suite_20261030_092726_743733`, `test_suite_20261030_093741_634314`, and `test_suite_20261030_093851_012162`. The [published gain excerpts](../data/README.md) contain 523 recorded feedback samples from the four gain-comparison moves, with source CSV hashes and a defined time origin.
