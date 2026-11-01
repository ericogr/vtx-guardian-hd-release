# Third-party components

The [LICENSE](LICENSE) covers only what the project owner wrote. The components below are not covered by it: each
stays under its own license, and its notice has to travel with any copy of it. The facts here were read from the
license files and manifests of each component on 2026-10-31.

## In the firmware (`.vgfw`)

| Component | License | Notes |
| :--- | :--- | :--- |
| ch32fun (`ch32v003fun`) | MIT | Linked into the firmware. Copyright 2023-2024 CNLohr et al. and 2021 Nanjing Qinheng Microelectronics (WCH). |

## In the CLI

The CLI is built from a locked set of Rust crates. Every crate in it is `MIT`, `Apache-2.0` or both
(`MIT OR Apache-2.0`), except these:

| Crate | License | Notes |
| :--- | :--- | :--- |
| `serialport` 4.10.1 | MPL-2.0 | Linked into the binaries and used unmodified; its source is at <https://github.com/serialport/serialport-rs>. The MPL-2.0 applies to its own files only. |
| `unescaper` 0.1.10 | MIT OR GPL-3.0-only | Used under the MIT option. |
| `unicode-ident` 1.0.27 | (MIT OR Apache-2.0) AND Unicode-3.0 | |

The CLI speaks the update protocol through `tinyboot-protocol` 0.5.2 (OpenServoCore, `MIT OR Apache-2.0`,
<https://github.com/OpenServoCore/tinyboot>). The license text and the copyright notice of each crate are in its
published source on <https://crates.io>.

## What was only read

**INAV** (GPL-3.0). No INAV source file is included: the MSP command numbers and frame layouts were read from its
source so that the CLI can talk to it. INAV itself is not modified or redistributed.
