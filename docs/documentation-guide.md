# How I keep this notebook

I want someone arriving halfway through the project to understand why I tried something, how I checked it, and what the result actually establishes. A list of test numbers or a pasted terminal log doesn't do that on its own.

## Two layers of documentation

Each system starts with a README explaining the current state. Hardware, interfaces, and practical workflow get their own pages when needed. Dated experiments preserve the route I took, including failed ideas and superseded settings. I update the overview when the answer changes, while keeping the old experiment's result intact and linking the newer finding.

My normal experiment structure is:

1. **The problem:** what I wanted to understand, and why it mattered to the vehicle.
2. **What I tried:** the hardware, starting state, commands, measurements, and comparisons.
3. **What I found:** the observations and recorded results, including failures.
4. **What I think it means:** my interpretation, alternatives, and practical consequence.
5. **What comes next:** the unresolved question or change I intend to make.

I write in first person because this is my work and my interpretation. I use direct instructions in a bench guide when that helps someone repeat a step. I keep exact parameters beside descriptive names: “omit the request for 100 ms” is more useful than “test 11 failed.” Historical case numbers can stay as a cross-reference, with the software version identified.

## Evidence and confidence

| Label | What I mean |
| --- | --- |
| Recorded | A saved capture or report contains the value; I name the source |
| Observed | I saw, heard, or roughly measured it, without a synchronized instrument record |
| Inferred | My explanation fits the observations but hasn't been isolated experimentally |
| Planned / checked offline | I've prepared the procedure or checked software behavior without demonstrating it on hardware |
| Unknown | I haven't established it |

These labels keep a plausible explanation from quietly becoming a specification. For example, my PSU clicked while the booster cycled, which makes power delivery a strong suspect; I haven't captured the voltage transient that would prove a reset cause.

## What I publish

I prefer small, useful data excerpts with units, time origins, calibration, source filenames, and hashes. A reader should be able to distinguish host-queued commands, adapter echoes, and actual feedback. If I haven't published the full capture or controller source, I say so rather than implying complete reproducibility.

I keep private paths, credentials, chat exports, and unrelated working files out of the repository. External research gets a link and a note about which hardware generation it covers. I'll identify source licenses before importing code or substantial third-party material; a reference link doesn't grant me permission to republish it. I haven't selected a repository-wide license yet.

Use the [experiment template](../templates/experiment.md) for the next session. This format is a guide to explaining the work, not a requirement to turn every small observation into a long report.
