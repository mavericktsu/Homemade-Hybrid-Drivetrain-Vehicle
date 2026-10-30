# Adding to this repository

Keep the root README as the introduction: what the vehicle is, how it works, what it aims to do, and where to read more.

## Grow by system

Use `systems/<system-name>/README.md` as the entry point for each subject. It should explain purpose, current status, useful findings, open questions, and links to detail.

The first subject is [Gen 1 iBooster](../systems/ibooster-gen1/README.md). Future subjects might include the P2 mechanical stack, battery, hybrid controls, cooling, or displays. Create those folders when their first useful document is ready.

As a system grows, add only what it needs:

```text
systems/<system-name>/
  README.md
  hardware.md
  lessons-learned.md
  experiments/
    YYYY-MM-DD-short-description.md
  software/
  data/
  images/
```

The example above is a pattern, not a list of folders that must all exist.

## Keep conclusions and history connected

The system README answers "where are we now?" A dated experiment explains what was tried and what happened. The lessons page collects useful conclusions and links back to the experiments.

Use the [experiment template](../templates/experiment.md). Record the exact unit, setup, software revision, request, observation, and limits of the conclusion. Preserve failed and inconclusive runs. Add a correction when a later test changes the interpretation.

Useful evidence labels:

| Label | Meaning |
| --- | --- |
| Target / intent | What the project aims to achieve |
| Manufacturer-specified | Published value with a direct source and applicable revision |
| Observed | Recorded behavior on the identified setup |
| Calculated / simulated | Derived result with inputs and assumptions |
| Inferred | Interpretation that still needs confirmation |
| Unknown | Not established |

## Code and evidence

Keep software near the system it serves. Give it a README with dependencies, version, offline checks, and limitations before publishing. Link each experiment to the exact code commit and configuration used.

Put selected raw captures, processed tables, or plots beside the relevant system and link them from the experiment. Record units, time bases, calibration, and processing steps. A screenshot alone rarely explains a result.

Use small, informative files in Git. Add large recordings or datasets as release assets or in an appropriate external archive, with a stable link and checksum. Decide whether substantial CAD belongs here or in a separate design repository before importing it.

Credit external projects and pin source revisions where possible. Link third-party manuals and code; check their license before copying or redistributing them. This starter does not choose a license for the owner's work.

Use Issues for unresolved questions or work items. When a question is answered, put the conclusion in the relevant document and link the issue. An issue thread should not be the only place a useful result is recorded.
