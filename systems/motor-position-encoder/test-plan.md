# Magnetic encoder: planned bench characterization

This is the plan for comparing magnet geometry before committing to a full rotating ring. I have **not** yet collected test data for this fixture.

## What I'm trying to establish

With a fixed Hall sensor and a sliding magnet strip, I want to measure where each digital transition actually happens. An analog Hall sensor can show the underlying field and help separate a bad magnet arrangement from a latch-threshold issue.

I am starting with **6 mm diameter × 2 mm thick, axially magnetized N42SH** discs arranged N/S/N/S. The intended final geometry is a 20-pole circular encoder on a 10-pole-pair motor.

## Linear pitches to compare

A 20-pole ring has an 18° mechanical magnet-center separation. The straight-strip approximation uses the arc pitch **2πr/20**:

| Equivalent ring pitch radius | Linear pitch |
| --- | --- |
| 40 mm | 12.57 mm |
| 50 mm | 15.71 mm |
| 60 mm | 18.85 mm |
| 70 mm | 21.99 mm |
| 80 mm | 25.13 mm |

The linear fixture has no ring curvature or wraparound, so I'll avoid drawing conclusions from the end magnets. I expect to use at least nine magnets per strip and analyze the middle transitions.

## Proposed instrumentation

- **Stationary DRV5013AG** digital bipolar latch, using an appropriate pull-up.
- **Linear Hall sensor** alongside it (DRV5055 candidate), with sensing-axis alignment, range, and offset checked against its datasheet.
- **Arduino Nano** for position, transitions, analog measurements, and USB serial output.
- **Linear position encoder or scale**, resolution and accuracy to be selected from a measurement-error budget.
- **Python** to save raw readings and compare switching positions across runs.

An external ADC is a possible improvement, not a prerequisite to start. I want synchronized position/event records rather than assuming a hand-driven carriage moves at a constant speed. The Nano's firmware will need to avoid interrupt and serial-buffer issues.

## Measurements and comparisons

1. Set magnet pitch and sensor air gap; document both.
2. Reference the carriage position and record repeated forward and reverse passes.
3. Record individual Hall transitions against position, plus analog field versus position.
4. Compare transition-to-transition spacing with nominal pitch, excluding end effects.
5. Compare repeatability across passes and the forward/reverse offsets caused by latch hysteresis.
6. Repeat for different gaps and strips, watching for missed transitions, saturation, and weak field gradients.

Sensor-to-sensor physical offsets will need calibration before comparing analog zero crossings with digital latch events. Raw voltage is not calibrated magnetic flux density unless the sensor transfer function and supply/reference are accounted for.

## What this experiment cannot prove

The fixture does not reproduce the exact magnetic field of a closed circular ring. It also doesn't validate inverter decoding, startup commutation, dynamic timing, thermal drift in operation, balance, structural retention, or 7,500 RPM performance.

I'll select a promising arrangement with the rail, then test the **actual ring and three-channel PCB** before treating it as an operational motor-position system.
