# Evidence I've included

I've published a small selection of my recorded results so the notebook has more behind it than conclusions. These are derived excerpts, not the full CAN captures or a replacement for the working helper. I generated them from saved local reports; no hardware was operated to prepare this documentation update.

| File | Contents |
| --- | --- |
| [Gain-response excerpts](gain-response-excerpts.csv) | 523 feedback samples from four 0–40% applies: gains 20, 32, 48, and a gain-48 repeat |
| [Gain-response summary](gain-response-summary.csv) | Transit, settling, overshoot, and original case verdict for those four applies |
| [Endpoint summary](endpoint-summary.csv) | Thirteen probes, their request/limit settings, plateau values, and post-probe response checks |
| [Run manifest](run-manifest.json) | Source capture/report SHA-256 hashes, recorded helper versions and source-file hashes, calibration, and provenance notes |

## How to read the gain excerpts

Each row identifies a trace, source session, historical case number, gain, elapsed seconds, raw position, and normalized position. Time zero is the **first host-queued enabled request**, not an independently captured physical CAN edge. Each excerpt covers up to the first two seconds or the end of the apply, whichever came first. There are no interpolated samples.

The samples show the lower-gain response and the start of high-gain hunting. They don't contain the full ten-second failed attempts. The summary metrics come from the complete saved reports, so their observation window differs from the excerpt. A blank settled time means settling wasn't observed before the move ended; it doesn't mean zero seconds.

## How to read the endpoint summary

The request min/max and limit-field values were decoded from **host-queued transmit records**. They establish what my helper queued, not what a second analyzer independently saw on the wire. `fixed_flow_raw` is blank for position-loop probes. `maximum_raw` and normalized percent come from booster feedback. The repeated endpoint wasn't the requested 60% target being reached: the probe deliberately terminated on a plateau.

`response_check_passed` means the subsequent small 0–5–0% check succeeded for that probe. It doesn't prove recovery from the separately induced command-loss fault.

## Calibration and availability

All percentages use my manually observed **320–2961 raw** span. They aren't an output-shaft millimetre calibration. Times are host timestamps, pressure/current weren't measured in these data, and the assembly wasn't under clutch load.

The manifest's filenames identify retained source files; the full raw files aren't included in this repository. Their hashes let me identify them unambiguously if I publish them later. Helper source hashes identify the files used in a run, not public Git commit IDs. I haven't imported the private working software, source captures, screenshots, or chat transcripts as part of this update.
