# 2026-11-05 — Looking for another remote-control mode

This entry brings together the follow-up work from November 2–5. The short
answer is that I can control the booster, but I still have not found a command
that moves it past the repeatable remote-travel ceiling. I learned quite a bit
about what the limit is *not*, and about separating ECU behavior from problems
in my own bench software.

## The problem

I want to use this unit to engage and disengage an engine-disconnect clutch.
The [earlier bench work](2026-10-30-endpoint-characterization.md) established a
remote endpoint at **1579 raw**, or **47.67%** of my manually observed
320–2961 feedback range. Physical output motion stopped too, although manual
operation could go farther. Raising the target, request magnitude or the
source-derived pressure-limit field had not extended it.

I wanted to distinguish three possibilities: another request encoding, missing
vehicle/pressure context, or an additional message outside the community map.
Those were hypotheses to test, not evidence that an unlock necessarily exists.

## My starting setup

I used the same **Tesla Gen 1 P1045523-00C**, DSD TECH SH-C30A interface and
DPS3010U supply described in [hardware](../hardware.md). The later work was
unloaded with the master cylinder removed. I had also observed the ceiling
with the master attached, so removing it did not remove the limit.

Percentages still mean normalized CAN feedback, not millimetres or clutch
release. I did not add synchronized pressure, force, temperature, current or
independent output-position instrumentation. The [earlier speed results](2026-10-30-speed-duty-and-ladder.md)
remain separate: these follow-up moves used deliberately gentle commands.

My computer closes the position loop around the native flow-like request;
I have not discovered a native absolute-position command. For the gentle
comparison tests I used gain 2, apply offset capped at +128 raw request units,
return magnitude capped at 64 and offset slew limited to 128 units/s.

## What I tried and found

### 1. Compare the request qualifier near the actual ceiling

The original source treats 38C byte 5 bit 4 as a boolean. I tested the pair of
bits 4–5 as candidate values rather than assuming the neighboring bit was unused.
At low demand, patterns 1 and 2 both moved; pattern 3 did not. Adding bit 6 or
bit 7 to the ordinary request produced no obvious difference at that low demand.

I then compared patterns 1 and 2 at 5%, 20%, 40%, 45% and a 50% target, with
ordinary response checks and an ordinary 50% repeat. All eleven gentle cases
completed. The three endpoint attempts tell the important part:

| Request | Requested target | Peak feedback | Result |
| --- | ---: | ---: | --- |
| Ordinary pattern 1 | 50%, raw 1640 | 1579 | Plateau |
| Candidate pattern 2 | 50%, raw 1640 | 1579 | Same plateau |
| Ordinary pattern 1 repeat | 50%, raw 1640 | 1579 | Same plateau |

The feedback cutoff was raw 1693, so it was not my software cutoff ending these
moves. The final outgoing demand was still positive, +122 above neutral. The
gentle 10–90% transit figures were about 9.47–9.48 seconds; those are not maximum
speed measurements. Pattern 2 is another working encoding on my unit, but this
simple swap did not unlock more travel. I have not assigned it an OEM mode name.

### 2. Ask the ECU for identity and faults

Vehicle-bus diagnostic requests at **0x64D** received matched replies at
**0x65D**. Read-data identifiers returned:

| Identifier | Returned data | What I can say |
| --- | --- | --- |
| F181 / F191 | `1045523-00-C` | Application/hardware identification strings |
| F194 | `8626708010401` | Supplier software identifier, ASCII |
| F195 | `10 11 08` | Raw supplier-version bytes; encoding unverified |

F182, F187, F188 and F189 returned `7F 22 31` under the tested conditions. The
initial DTC list contained ten records, four with the test-failed bit set;
the others included history bits. I did not treat all ten as new active failures.

Fault snapshots were rejected as unsupported in this context. Extended records
were returned for all ten baseline DTCs: each had a seventeen-byte body, starting
with record identifier 01. I preserved them without inventing sensor names,
units or event timing for the remaining bytes.

I also captured all eight 35D fault-matrix pages. Their 221 field names come
from a legacy community DBC, with a declared-DLC inconsistency, so I keep the
interpretations labelled candidates. I have not established a complete mapping
between those names and every numeric UDS DTC. No fault clearing, diagnostic
writes, calibration routines or firmware changes were used in this work.

### 3. Observe diagnostics while moving, using one adapter

I joined the vehicle and private buses experimentally so the one adapter could
see both. That let me retain the handshake while making diagnostic reads. It
also exposed a real complication: the vehicle's event-summary burst uses
**0x38D**, the same identifier as a private handshake message.

The first combined moves returned successfully, then my script stopped on the
received summary. The recorded private state was still READY; that particular
displayed error was my guard, not a decoded new ECU fault. I initially retired
the combined experiment too broadly, then continued with bounded one-shot and
stepped observations that preserved useful data before the stop.

I later taught the helper to recognize the observed ordered
`31D → 34D → 36D → 37D → 38D` summary signature, log it and continue. Unexpected
command traffic and actual CAN/feedback failures still stop. This does not
prevent simultaneous conflicting transmissions on the physical joined network.
One adapter also cannot attribute a received frame to one original port.
Separate CAN channels remain the way to retain that distinction.

### 4. Follow the runout and ignition-context clues

An undecoded runout candidate in 38F changed before the remote endpoint. I
made one continuous gentle application, holding at 39%, 41%, 45% and a requested
50%, then returning once. Every hold lasted about 8.5 seconds and included
entry/late DTC reads and all matrix pages.

| Requested target | Held raw range | Runout during neutral commanded flow | Longest neutral interval |
| --- | --- | --- | ---: |
| 39% | 1345–1346 | 85 | 4.18 s |
| 41% | 1398–1399 | 0 | 8.40 s |
| 45% | 1503–1505 | 0 | 3.88 s |
| 50% | 1579 | No neutral comparison; demand remained +122 | — |

The runout transition appeared around raw 1387, about 40.4%, and returned to
85 near raw 1323 on release. It stayed zero through the sustained neutral-flow
holds above the transition. That weakens the idea that it simply reflects my
currently positive flow command. It does not establish its physical meaning
or show that it causes the endpoint; neutral commanded flow is not a measure
of internal motor effort or hydraulic pressure.

All 1701 paired hold samples reported candidate READY. All twelve diagnostic
transactions were positive. The familiar 02FE00 status change, `28 → 29`, was
already present at the first hold; later lists and the four complete matrices
matched. No new recorded fault marked either the runout transition or plateau.

Separately, I compared the source-derived 38B ignition bit at 0 and 1 while
physical ignition stayed on. The transmitted byte changed from `01` to `09`,
with updated CRC. Both completed stationary tests still showed the same d055
ignition-plausibility indication and 02FE00 status change. An earlier incomplete
run stayed in state 4 and is not counted as a successful comparison. The simple
one-bit ignition explanation was not supported by the completed pair.

### 5. Try messages outside the known map

A community DBC is not a catalogue of everything firmware can receive. I built
a bounded survey rather than treating the known map as exhaustive:

| Batch | Candidate IDs | IDs × families | Completed cases |
| --- | --- | ---: | ---: |
| Original neighborhood | 0x380–0x38A | 11 × 2 | 22 |
| Upper neighborhood | 0x390–0x39F, excluding known 0x39D | 15 × 2 | 30 |
| Lower neighborhood | 0x370–0x37F, excluding known 0x37D | 15 × 2 | 30 |
| Total | 41 distinct IDs | | **82** |

For each ID I tried two eight-byte payload hypotheses, with the reference CRC
and a modulo-16 counter:

```text
Disabled-request shape: CRC counter F0 00 E0 07 00 00
Counter/zero shape:     CRC counter 00 00 00 00 00 00
```

“Disabled-request shape” describes the source of the bytes. It does not mean
OFF when sent under an unknown ID. I kept the known 38C motion request disabled,
secured the unloaded assembly and retained a physical power disconnect.

After a settled baseline, each case sent fifty frames over one second, then
observed for at least 8.5 seconds and collected a DTC reply and all eight matrix
pages. The helper watched new IDs/formats, private feedback, diagnostics, matrix
changes and later the previously constant bits of existing 32D/33D broadcasts.
It skipped IDs already transmitting in baseline. A detected change paused the
batch; Auto otherwise continued one candidate at a time.

All **82 completed cases**, comprising **4100 probe packets with matching owned
adapter echoes**, finished with **no detected change**. Position stayed raw 320
and state stayed READY during probing. Post-probe DTC lists matched the settled
baseline and no post-Go matrix changes were recorded. Partial attempts are
excluded from those totals and counted only through their completed repeats.

Some longer runs stopped on echo-backlog guards at different IDs; resumed cases
completed. I found substantial repeated diagnostic data in live report encoding
and reduced it from roughly 577 kB to 47 kB on one saved sample. Encoding time
fell from about 10.7 ms to 0.8 ms in that offline comparison. Full detail stayed
in the context sidecar. Both full lower-neighbor runs then completed without a
guard, which is encouraging but does not prove reporting caused every earlier
interruption. I did not relax the timing, backlog or motion guards.

## What I think it means

The useful outcome is a better-characterized actuator, a repeatable endpoint,
a working diagnostic path and clearer fault/recovery distinctions. I have not
found a full-travel mode, and I should not make clutch packaging depend on one.

The 82 negative cases are limited evidence: two payload shapes, one CRC/counter
hypothesis and short observation windows on one firmware variant. Adapter echoes
do not demonstrate application acceptance. Auto candidates share an ECU session,
and a hidden state change might produce no at-rest feedback. I did **not** follow
each discovery probe with a travel comparison, so this survey does not show that
every probe left the reachable endpoint unchanged.

The bench also still has distinct operational issues. A USB access-denied error,
an empty receive capture, a host echo backlog and fresh READY feedback with
ignored external motion are different observations. My pin-17 recovery finding
and suspected PSU issue remain documented in the earlier entries; none becomes
a universal ECU diagnosis because one new test completes.

## Where I am leaving the investigation

For the build, my supported working assumption remains the measured remote
envelope, with a 45% normal target ceiling and gentler release. Clutch force,
hydraulic displacement, loaded response and endurance still need validation.

If I revisit the mode search, the useful next experiment is a gentle reference
application, a selected stationary probe, then an identical application while
keeping the handshake continuous. That could expose a silent change in reachable
travel. It is a proposed experiment, not a completed result or a prepared new
controller. A genuine Gen 1 capture during vehicle-requested braking would also
be more informative than assuming nearby unknown IDs use our packet format.

## Evidence and references

The [derived summary and source manifest](../data/remote-control-investigation-summary.json)
contains the endpoint/hold numbers, discovery coverage, source basenames and
SHA-256 hashes. Full captures and the working controller remain local; this is
a notebook and selected evidence, not a download-and-run controller release.

I used the [pinned Gen 1 Ocelot code](https://github.com/RetroPilot/ocelot/blob/94efc67349227b7a4dc410c585c9dee507a5960e/firmware/ibst/main.c),
the [legacy Model S fault-field database](https://github.com/BogGyver/opendbc/blob/9c0b6fe3ae205beccb879635750150620b8f7f9b/tesla_can.dbc)
and [independent Gen 1 bench observations](https://github.com/pelosim/tesla-ibooster-can/blob/main/docs/BENCH_LOG.md).
Other-generation [Rivian actuator definitions](https://github.com/commaai/opendbc/blob/178c81ab5c052ac92a849f6400703d082d268c02/opendbc/dbc/rivian_primary_actuator.dbc)
helped form the qualifier hypothesis, but did not supply an interchangeable
Gen 1 command. The commented-out 391 receive fragment in Ocelot likewise did
not establish a native mode-selection message. More source context is in
[references](../references.md).
