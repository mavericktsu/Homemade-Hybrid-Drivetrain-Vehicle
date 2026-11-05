# References I used

I keep generation and part-number differences visible because a useful idea from another unit isn't automatically a valid command or calibration for mine.

| Source | How I used it / its limits |
| --- | --- |
| [RetroPilot Ocelot Gen 1 implementation, pinned revision](https://github.com/RetroPilot/ocelot/blob/94efc67349227b7a4dc410c585c9dee507a5960e/firmware/ibst/main.c) and [CRC header](https://github.com/RetroPilot/ocelot/blob/94efc67349227b7a4dc410c585c9dee507a5960e/firmware/crc.h) | Reference message set, counters, CRC, and flow-like request packing. Community code, not an OEM specification or measured calibration for my unit. |
| [Fast and Quiet Gen 1 pinout](https://www.fastandquiet.com/downloadable/bosch-ibooster-gen-1-pinout.PDF) | Connector/power reference, considered alongside my bench wiring. Connector orientation and variants still matter. |
| [Bosch iBooster operating principle](https://www.bosch-mobility.com/en/solutions/braking-systems/ibooster/) | Manufacturer explanation of motor-assisted operation and travel sensing. It doesn't document my reverse-engineered CAN fields. |
| [Bosch summary PDF](https://www.bosch-mobility.com/media/global/solutions/passenger-cars-and-light-commercial-vehicles/driving-safety-systems/brake-booster/ibooster/summary-ibooster.pdf) | General product information, including the 9.8–16 V main range; not exact-part reset thresholds or a clutch qualification. |
| [pelosim Gen 1 bench log](https://github.com/pelosim/tesla-ibooster-can/blob/main/docs/BENCH_LOG.md) and [decode notes](https://github.com/pelosim/tesla-ibooster-can/blob/main/docs/DECODE.md) | Useful read-only sensor research on a different Tesla part, 1037123-00-B. I don't transfer its millimetre calibration or treat it as my command implementation. |
| [Gen 2 research kit](https://github.com/ThatguyCplus/ibooster-can-research-kit) and [OVCS iBooster notes](https://github.com/open-vehicle-control-system/dbc/blob/main/ibooster/README.md) | Comparisons and research questions. Gen 2 lengths/flags aren't interchangeable with my eight-byte Gen 1 messages. |
| [DSD TECH SH-C30A documentation](https://github.com/dsdtech-official/can-adapters/tree/main/SH-C30A) | Adapter-specific setup information. |
| [Kvaser CAN protocol tutorial](https://kvaser.com/can-protocol-tutorial/) | Why ACK is a link-level observation, not proof that the booster accepted a movement request. |
| [WANPTEK DPS-series page](https://www.wanptek.com/productinfo/1171926.html) and [DPS manual mirror](https://device.report/m/fb615a27c49f7ea4f21c3143724ed9910adf86004df6302ee68ae5d9479e1eb9_optim.pdf) | Supply specifications and protection behavior. They don't prove which protection state, if any, occurred on my bench. |

The November follow-up also used the [pinned legacy Model S DBC](https://github.com/BogGyver/opendbc/blob/9c0b6fe3ae205beccb879635750150620b8f7f9b/tesla_can.dbc) for candidate 35D fault fields and the [Rivian actuator DBC](https://github.com/commaai/opendbc/blob/178c81ab5c052ac92a849f6400703d082d268c02/opendbc/dbc/rivian_primary_actuator.dbc) as a request-qualifier research lead. The legacy file declares DLC4 while its fault fields extend through bit63; my captures are DLC8. The Rivian payload layout differs from Gen 1. Neither source is a verified override map for my ECU. The [follow-up entry](experiments/2026-11-05-remote-travel-and-message-discovery.md) separates those source clues from the actual bench results.

I link external research rather than republishing its code or documents. These references were consulted for the October 2026 work; the pinned Ocelot link fixes the implementation revision used for the protocol explanation.

