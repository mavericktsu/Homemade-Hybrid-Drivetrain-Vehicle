# My bench hardware and wiring

I used this setup for the experiments in this section. These details identify the bench I actually tested, rather than a universal wiring diagram for every Bosch iBooster.

| Item | My setup |
| --- | --- |
| Actuator | Bosch Gen 1, Tesla P1045523-00C / 1045523-00-C |
| CAN interface | DSD TECH SH-C30A USB-CAN, candleLight/gs_usb interface |
| Computer | Windows, Cangaroo for listening and a custom Python helper for command tests |
| Supply | WANPTEK DPS3010U, nominal 0–30 V / 0–10 A, used around 12–13 V |
| Mechanical state | Secured assembly, no clutch load; master attached in earlier tests and removed for the November follow-up |
| Hydraulics | Earlier attached-master setup was reported filled/bench-bled; later removed-master tests had no hydraulic circuit under test |
| Measurements | CAN feedback and host logs; rough physical stroke checks; PSU front-panel readings |

I don't have synchronized voltage, current, pressure, temperature, or independent output-position instrumentation in these captures. The supply's 10 A rating isn't a record of the current-limit setting I used. Displayed current was generally low—roughly 1–2 A during hard, fast manual operation and often under 1 A during remote movement—but I haven't measured short peaks or loaded clutch demand.

## Connections on this bench

| Connector cavity | Connection I used | Purpose / note |
| --- | --- | --- |
| 1 | Permanent +12 V through the 40 A fused branch | Main motor-power feed |
| 9 | Supply negative / ground | Common reference, including the CAN adapter reference |
| 17 | Permanent +12 V through a 5 A fused branch | Electronics feed I later cycled to reboot the unit |
| 20 | Maintained switched +12 V through a separate 5 A fused branch | Ignition / wake input |
| 18 | CAN-H | Private/Yaw CAN used for external control |
| 10 | CAN-L | Private/Yaw CAN used for external control |
| 25 / 16 | Vehicle CAN-H / CAN-L | Vehicle/diagnostic bus; initially separate, later experimentally joined for combined observations |

I fed the power branches from the same supply, with a common negative. The ignition input needs to stay connected during normal testing: touching it briefly wakes the unit, but doesn't reproduce a maintained ignition-on state. Cycling pin 17 worked as a reboot on my bench; that doesn't prove the internal distribution of every power domain.

Identify cavities by their molded numbers and confirm the connector viewing direction before wiring. I used the [Gen 1 pinout reference](https://www.fastandquiet.com/downloadable/bosch-ibooster-gen-1-pinout.PDF) alongside bench observations. Don't mix one wire from the vehicle bus with one from the Yaw bus, or assume the same pinout applies to a different connector/generation.

## CAN setup

The command bus is **500 kbit/s, standard 11-bit CAN**. I use one CAN application at a time so Cangaroo and the helper don't compete for the USB device. Closing a capture window isn't always the same as releasing the interface; earlier “Access denied” errors were a separate host-side problem.

Check termination with power off. Two 120 Ω end terminations normally measure about 60 Ω between CAN-H and CAN-L; I haven't retained a measured termination value for this bench. A CAN interface receiving in normal mode can still acknowledge frames even when its application isn't transmitting commands. Listen-only mode behaves differently, so “all sender traffic stopped” isn't equivalent to physically disconnecting CAN.

## Later combined-bus experiment

For the November follow-up I experimentally joined **H25 + H18 to adapter H** and **L16 + L10 to adapter L**, with GND9 as the common reference. The setup retained an external 120 Ω termination at each leg/end and adapter termination OFF. This was an unloaded research arrangement for one adapter, not a vehicle-network architecture.

It exposed a shared identifier: a vehicle-summary 38D frame differs from the private 38D handshake. Recognizing its observed burst lets my software log and continue, but cannot prevent simultaneous conflicting transmissions or tell which original port accepted a message. Isolated channels retain those distinctions. The [testing summary](experiments/2026-11-05-remote-travel-and-message-discovery.md) records the resulting guard stops and interpretation.

I observed the same remote ceiling with the master attached and removed. That argues against the attached master being the sole explanation; it does not establish the internal cause or available clutch force.

## How I defined travel

I manually swept the rod and recorded feedback from **320 raw at rest to 2961 raw near full manual travel**. There was some bounce at the far end, so this is an observed span, not a precision mechanical calibration.

```text
span = 2961 - 320 = 2641 raw counts
percent = 100 × (raw - 320) / 2641
target_raw = 320 + round(2641 × target_percent / 100)
```

| Point | Raw feedback / target |
| --- | ---: |
| Rest / 0% | 320 |
| 40% target | 1376 |
| 45% target | 1508 |
| 47% protective cutoff used in the newer profiles | 1561 |
| Repeated remote plateau | 1579, or about 47.67% |
| Manually observed upper end | 2961 |

The helper's upper cutoff is a software guard, not a mechanical stop. Manual input and motor-driven output don't necessarily have identical usable travel. My rough 14–17 mm output measurement at 40% isn't enough to convert all raw values into millimetres, hydraulic volume, or clutch release.

