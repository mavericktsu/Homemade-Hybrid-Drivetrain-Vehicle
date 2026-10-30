# Gen 1 Bosch iBooster development

Investigating external control of a Gen 1 Bosch iBooster as a potential **hydraulic clutch actuator** for the P2 hybrid drivetrain.

The unit is a Tesla Model X donor **1045523-00-C**. Existing work includes a Windows USB-CAN bench tool, startup testing, motion profiles, recovery experiments, and endpoint characterization. This repository initially publishes a summary; the tool and original captures remain in the working project.

## Current understanding

- A maintained-ignition bench run established sustained feedback and the reference readiness state.
- Motion to a target at 40% of the manually observed feedback span has been demonstrated.
- Repeated 60% requests plateaued near **47.7% of that span**, while requests and feedback continued. The owner also reported that physical output motion stopped.
- Increasing position-loop gain did not improve the observed transit time in the compared runs and introduced sustained oscillation at the highest tested gain.
- New endpoint-characterization and automatic software-recovery cases have been prepared. Their preparation does not establish a successful hardware result.

These are observations on one setup. Feedback percentages are not calibrated physical stroke or clutch-release percentages. Clutch force, displaced fluid volume, loaded behavior, installation, and service reliability remain unresolved.

## Read more

- [Hardware and test context](hardware.md)
- [Lessons learned](lessons-learned.md)
- [Experiment index](experiments/README.md)
- [Startup findings](experiments/2026-10-29-startup.md)
- [Travel and gain findings](experiments/2026-10-30-travel-and-gain.md)

## External reference

The original bench implementation drew on [RetroPilot Ocelot's Gen 1 implementation at commit 94efc67349227b7a4dc410c585c9dee507a5960e](https://github.com/RetroPilot/ocelot/blob/94efc67349227b7a4dc410c585c9dee507a5960e/firmware/ibst/main.c).

That is a community implementation, not a Bosch specification for this donor unit. This summary provides no new pinout or executable motion instructions.
