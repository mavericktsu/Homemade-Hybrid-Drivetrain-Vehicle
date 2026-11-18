# How to read these notes

I'm using this repository to explain *why* I made the choices I did, not just record the part numbers I ended up with. A finished assembly can make the earlier decisions look obvious. Usually they weren't.

The easiest way in is the [project overview](../README.md), followed by the [system map](../systems/README.md). The [baseline](project-baseline.md) reflects the current direction when an older test or drawing says otherwise.

## Overview first, experiments second

For each subsystem I try to keep two levels of detail:

- **Overview:** what I'm trying to do, why it's difficult, what I've chosen, and what still isn't answered.
- **Working notes:** hardware details, test procedures, measurements, and dated experiments. These are where I put the failed ideas and the irritating little discoveries that tend to matter later.

The [iBooster section](../systems/ibooster-gen1/README.md) is a good example. There's a readable overview, but the [experiment index](../systems/ibooster-gen1/experiments/README.md) still has the full path through the testing.

## How certain is a result?

| Term | What I mean |
| --- | --- |
| **Recorded** | I saved an instrument capture or other data that supports the statement. |
| **Observed** | I saw, heard, or roughly measured something, but don't necessarily have a synchronized record. |
| **Inferred** | The explanation fits what I've seen, but I haven't isolated the cause. |
| **Planned** | A design or test I intend to try. It is not a result. |
| **Unknown** | I haven't established it. |

I don't always repeat these labels in every sentence, but I try to make the distinction clear. For example, hearing a bench power supply click during a failure makes it worth investigating the power supply. It doesn't prove what happened electrically.

A claimed 'test passed' also isn't much use if the hardware didn't actually do the thing I was testing. For actuator work, I care about commanded movement **and** feedback or physical response.

## What's in the repository

I publish the explanations and selected data that are useful to understand or reproduce a result. When available, that includes units, timestamps, capture identifiers, configuration, and enough context to tell a real response from a command or an adapter echo.

This isn't a dump of every CAD revision, raw log, or script on my computer. If the data or source for a result isn't published, I'll say so. I also keep private paths, credentials, and unrelated material out of the repo.

For new bench work, there's an [experiment template](../templates/experiment.md). It's there to keep the important details from getting lost, not to make every five-minute test read like a journal paper.
