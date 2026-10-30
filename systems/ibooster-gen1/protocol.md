# What my position command actually does

The most useful clarification in this work is that I'm **not sending a verified native absolute-position command**. My helper reads position feedback, compares it with a target, and sends a signed, flow-like request. The position controller is on my computer.

I based the Gen 1 message set on the [pinned Ocelot implementation](https://github.com/RetroPilot/ocelot/blob/94efc67349227b7a4dc410c585c9dee507a5960e/firmware/ibst/main.c), with its [CRC implementation](https://github.com/RetroPilot/ocelot/blob/94efc67349227b7a4dc410c585c9dee507a5960e/firmware/crc.h). This is community reverse engineering, not a Bosch interface specification. The following describes the reference used in my bench work, not a standalone actuator controller.

## Message set

All messages below use standard 11-bit IDs on the private/Yaw bus. In this Gen 1 implementation the three transmitted messages have eight data bytes.

| ID | Reference rate | Role in my helper |
| --- | --- | --- |
| 0x38C | 100 Hz | External request, enable/quality bit, flow-like quantity, limit field |
| 0x38D | 100 Hz | Companion reference message; shared 4-bit rolling counter with 0x38C |
| 0x38B | 50 Hz | Companion message with its own 4-bit counter |
| 0x38E | Received | Position-feedback candidate and integrity checks |
| 0x38F | Received | Mode/status and driver-input candidates |

In the reference, byte 0 is a CRC over bytes 1–7: polynomial 0x1D, initial value 0xFF, final XOR 0xFF. Byte indices here start at zero. Counters wrap modulo 16; a repeated payload with an old counter isn't a fresh command.

The reference disabled/neutral templates, before inserting counters and calculating CRC, are:

```text
0x38C: [CRC, counter, 0xF0, 0x00, 0xE0, 0x07, 0x00, 0x00]
0x38D: [CRC, counter, 0x00, 0x00, 0x00, 0x00, 0x20, 0x00]
0x38B: [CRC, counterB, 250, 1, 0, 1, 0, 0]
```

These templates explain my baseline, not an instruction to send one frame by hand and expect reliable operation. Correct scheduling, counters, CRCs, receive checks, and shutdown handling all matter.

## Request and feedback fields

For 0x38C, the reference packs a 16-bit request beginning at bit 28:

```text
q = (byte3 >> 4) | (byte4 << 4) | ((byte5 & 0x0F) << 12)
neutral q = 0x7E00
external-request enable / quality = byte5 bit 4
```

My proportional loop adds a signed offset to neutral:

```text
offset = clamp(gain × (target_raw - measured_raw), -flow_cap, +flow_cap)
q = 0x7E00 + offset
```

I use **raw request units** for the cap. The reference's physical-unit comments aren't a calibrated flow measurement on my part number. A cap of ±5120 gives request values from 0x6A00 to 0x9200. Changing gain changes how strongly the host reacts to position error; changing the cap limits the request magnitude.

The reference limit field uses a value of 120, encoded doubled across byte 2 and byte 3 bit 0. My endpoint experiments varied that field, but I didn't measure pressure, so I can't infer its loaded behavior from the unchanged unloaded endpoint.

My position candidate is `byte3 | ((byte4 & 0x0F) << 8)` in 0x38E. My mode candidate is `(byte2 >> 3) & 7` in 0x38F; byte 2 bit 0 is a driver-input candidate. I use the community descriptions **7 / READY** and **4 / STANDALONE** for orientation. Neither is proof that an enabled request is accepted: I have seen fresh mode-7 feedback while external motion was unavailable.

## Why 0x38C matters

It carries the movement request and its permission/quality information as a repeated, checked message. Calling it a constant enable signal misses the timing and counter requirements. My omission tests suggest a freshness boundary near the 100 ms region under their specific conditions, but don't identify the exact internal watchdog or isolate counter discontinuity from elapsed time.

Withdrawing the enable bit and returning the request to neutral is my normal cleanup. I haven't established a universal “stop sending and it always returns to rest” guarantee. In a clutch application I will need to monitor actual response and handle loss of control explicitly.

Finally, a host TX-queued record proves intent to transmit. An adapter echo confirms adapter handling. A CAN ACK confirms link-level reception by a node, **not application acceptance or actuator motion**; see the [Kvaser CAN explanation](https://kvaser.com/can-protocol-tutorial/). Echo notifications aren't inherently new commands bouncing back through the application.

Gen 2 research helped frame questions, but its payload lengths and flags aren't a drop-in Gen 1 protocol. I haven't verified another native command that overrides this unit's remote endpoint. See [references](references.md) for the different hardware each project studied.
