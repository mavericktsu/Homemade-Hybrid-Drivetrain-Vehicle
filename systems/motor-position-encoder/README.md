# Motor position: how I ended up making an encoder

The Beyond Motors AXM2 is the electric half of my P2 hybrid drivetrain, sitting between a small-block Chevy and a G-Force G101A. Packaging the motor in that stack has been a challenge by itself. I figured telling the inverter where the rotor is would be the easy part. Apparently not.

I originally planned to use a resolver. They're common in this sort of application, and I wasn't interested in reinventing one. Then I started looking at how I'd actually mount it. The AXM2 has a through-shaft, I'm already fighting for axial space, and the **DTI HV-500LC** has fairly specific resolver requirements.

At this point I'm leaning strongly toward a **custom three-Hall encoder** and a 20-pole magnet ring. I've laid out an initial PCB and am working out how to test magnet spacing without making a different ring for every idea. I haven't run it with the inverter yet.

## Why the sensor matters

The inverter needs rotor position to commutate the motor correctly. A speed signal alone won't do that, and a sensor that works electrically but introduces a large angle error isn't much of a solution either.

The AXM2 has **10 pole pairs**. That means electrical angle moves ten times as fast as shaft angle: a 1° mechanical error becomes **10° electrical**. With a motor rated to 7,500 mechanical RPM, small alignment and timing mistakes deserve attention.

The practical problems are:

- **Through-shaft packaging:** I can't simply put a conventional shaft-end resolver on the end of the motor without interfering with the drivetrain layout.
- **Axial length:** Every extra millimeter in the engine–clutch–motor–gearbox stack matters. I'd rather not solve a sensing problem by creating another transmission-tunnel problem.
- **Inverter compatibility:** The HV-500LC's resolver interface is intended for a Tamagawa-style **1X** resolver; an arbitrary resolver or digital encoder is not automatically compatible. The separate Hall interface still needs its pinout, electrical behavior, and firmware configuration confirmed on my particular inverter.
- **Temperature, vibration, and speed:** Whatever I mount on the rotor must survive the environment and centrifugal loads. A successful hand-rotation test is not permission to spin a printed magnet holder at 7,500 RPM.

## Trying to make a resolver work

A 1X resolver is appealing because it provides continuous angular feedback rather than discrete commutation states. My first serious option was an **Admotec Rotasyn RO10080-M-R060**, an annular resolver with a **60 mm bore** that could fit around the shaft instead of at its end.

I got far enough into this to work through a mounting concept around the motor flange: a roughly **61 mm shoulder**, a sleeve for the resolver rotor, a retention arrangement using a spacer and snap ring, and an adjustable stator mount to dial in alignment. Admotec provided mounting guidance; the excitation discussed was **7 Vrms at 10 kHz**. It looked workable on paper, but there was a fair bit of custom machining and alignment involved.

The more I looked at it, the more work there was: rotor retention, concentricity, stator alignment, and not much room between the resolver and the AXM2 face. The big hole in the resolver solved the through-shaft problem. It didn't solve the rest.

I also looked at **multi-speed resolvers**. An 8X resolver, for example, repeats its electrical pattern eight times per mechanical revolution. It isn't interchangeable with a 1X resolver merely because both are resolvers; the controller must understand which mechanical sector it's in. A simple interface box can't recover that missing absolute sector information from the repeated waveform alone.

I also considered using **1:1 gears** to drive a conventional resolver mounted off to the side. That moves the resolver out of the axial stack, but it trades one problem for gear backlash, torsional compliance, extra bearings, alignment, and a little mechanism running at motor speed. With ten motor pole pairs magnifying angle error, I didn't like introducing gear lash into the rotor-position reference.

I don't think any of those are necessarily bad solutions. I just kept adding complexity to a part of the build I really wanted to keep simple.

## Why Hall sensors started looking better

The alternative is a **20-pole magnetic ring** with alternating north and south poles and three stationary Hall latches. This matches the motor's ten electrical cycles per mechanical revolution.

Instead of a continuous resolver angle, the inverter sees a sequence of three digital Hall states. With the sensors **120° electrical apart**, the ideal pattern supplies six commutation sectors per electrical revolution.

For a 10-pole-pair motor, 120° electrical corresponds to **12° mechanical** sensor separation. Adjacent magnets on the 20-pole ring are **18° mechanical** apart.

The biggest benefit for me is packaging. The magnets can ride on an appropriately retained rotor-mounted ring, while a small sensor PCB sits near the ring face. There is no shaft-end device, no gear train, and comparatively little added axial length. And if I break a Hall sensor, replacing a tiny IC is a lot less painful than replacing a specialized resolver.

My initial PCB uses **three TI DRV5013AG bipolar Hall latches** with individual decoupling capacitors, a shared supply capacitor, and a five-position connector for 5 V, ground, and Hall A/B/C. The latches have **open-drain outputs**, so pull-up behavior and voltage levels need to match the inverter inputs. The board is a design exercise so far; it has not been demonstrated driving the DTI.

There is a pretty important catch: **three Hall latches do not give me high-resolution rotor position**. The ideal state changes occur every **6° mechanical** for this 10-pole-pair setup (60 combined transitions per shaft revolution), and the inverter must handle commutation/interpolation appropriately. Increasing ring diameter can reduce the angular effect of a given magnet placement error, but it does **not** add electrical resolution.

At 7,500 RPM, each Hall channel would output a 1,250 Hz cycle, with 60 combined state transitions per revolution. That frequency isn't the primary challenge; reliable angle accuracy, startup behavior, and inverter compatibility are.

## The magnet spacing problem

The first ring layout put **20 individual axially magnetized magnets** on an **80 mm pitch diameter** (40 mm radius). The three Hall positions were laid out at 12° mechanical intervals along that circle. The sensor board was designed in EasyEDA Standard with the sensing faces oriented toward the magnet ring; its design-rule check reported no errors.

That seemed straightforward until I started looking at the actual magnetic fields. Now I'm reconsidering both the magnet size and ring radius. A current candidate is a **6 × 2 mm N42SH neodymium disc**, still axially magnetized. The SH grade is appealing for temperature margin, but its suitability will depend on actual operating temperatures and field measurements.

The pitch radius is still open; **80 mm radius** is one geometry under consideration, not the same thing as the original **80 mm diameter** PCB layout. For a 20-pole ring:

| Pitch radius | Center-to-center arc pitch |
| --- | --- |
| 40 mm | 12.57 mm |
| 50 mm | 15.71 mm |
| 60 mm | 18.85 mm |
| 70 mm | 21.99 mm |
| 80 mm | 25.13 mm |

The magnetic field from adjacent discs overlaps. That isn't necessarily undesirable. A bipolar latch holds its state until it sees enough field of the *opposite* polarity, so I care about consistent switching positions, enough field margin across the air gap, and a steep enough field gradient where the latch changes state. I'm not trying to make the digital Hall outputs sinusoidal. I need the switching points to be consistent.

The larger radius makes manufacturing error less painful in angular terms. At 80 mm radius, a **0.5 mm tangential placement error** is about **0.36° mechanical**, or **3.6° electrical**. That's why the magnet carrier needs to be more than a circle with some convenient pockets.

I also investigated continuous multipole rings, including **radially magnetized** candidates from CCMagnetics and the possibility of a custom **axially magnetized** ring from SDM Magnetics. Radial rings would require a different sensor arrangement from my present face-reading PCB. A purpose-made axial ring might ultimately give more uniform pole geometry, but cost, lead time, mounting, and rotor retention still need answers.

## Testing the magnets without making five different rings

Rather than print several full rings and redesign the PCB every time I change the pitch, I want a **linear magnetic test fixture**.

Instead, I'm planning to slide a row of alternating N/S magnets under a stationary sensor. Swapping straight strips for different pitches should be a lot easier than building several rotating assemblies. A linear position encoder would measure where the carriage is, and an **Arduino Nano** would collect:

- The digital DRV5013 latch state and exact switching events.
- An analog Hall reading (a **DRV5055** is a candidate) to show the underlying magnetic field.
- Carriage position and timestamps, streamed over USB for Python analysis.

I'd compare repeatability, switching intervals, direction-dependent hysteresis, and sensitivity to air gap. The analog sensor is important because the DRV5013 tells me *when* it switched, not the shape or strength of the field that caused it.

The [linear test plan](test-plan.md) covers the fixture, instrumentation, and measurements in more detail. The rail should let me narrow down the spacing before I build a proper rotating ring. It cannot perfectly reproduce the curved, closed magnetic ring, or prove performance at rotational speed. The eventual complete ring will still need its own checks.

## What still needs to be proven

Hall looks like the best fit for my layout, but there are a few fairly important things I still need to prove.

| Question | Current status |
| --- | --- |
| Does the HV-500LC accept and correctly interpret this specific three-Hall arrangement? | **Not yet bench-verified**; electrical levels, configuration, phasing, startup, and direction must be confirmed. |
| Which pitch radius, magnet grade, and sensor air gap provide robust switching? | **Planned measurement**, not a completed comparison. |
| Can I manufacture and index a 20-pole ring precisely enough? | **Open**, including pole polarity, location error, and a repeatable mechanical reference. |
| How will magnets be retained at 7,500 RPM? | **Open structural design**. A printed carrier alone is not a validated retention solution. |
| Does the rotor remain balanced and thermally stable with the ring installed? | **Not tested**. |
| What happens during low-speed starts, reversals, missing transitions, or sensor faults? | **Needs inverter commissioning and fault testing**. |
| Is Hall-sector feedback sufficient for the torque control and smoothness I want? | **Needs practical validation**; a resolver may still be preferable if the inverter's Hall mode isn't adequate. |

## Where I am now

The annular resolver was the more conventional precision-feedback solution, and I spent a fair amount of time trying to make it fit. But fitting it meant custom mechanical parts, tight clearances, and several new ways to make the drivetrain harder to assemble. A gear-driven resolver was even less attractive.

The **custom Hall ring** is my chosen development direction because it puts the sensing hardware where I have room, uses a straightforward three-signal interface, and gives me control over the geometry. Its weak points—pole accuracy, commutation quality, inverter behavior, and high-speed retention—are real, and they're what the next experiments are intended to resolve.

So what started as 'pick a resolver' has become another little project. Not exactly the plan, but it does look like a cleaner way to package the drivetrain if the testing works out.

*Status: design and test planning as of November 18, 2026. No rotating encoder or inverter commissioning results are claimed here.*
