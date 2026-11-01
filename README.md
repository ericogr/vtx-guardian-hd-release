# VTX Guardian HD: releases

VTX Guardian HD is a small module that sits in line between an INAV flight controller and a digital HD VTX (Caddx Walksnail / Ascent). It switches the VTX supply on when the aircraft arms and off 5 s after it disarms; a button switches it by hand.

This repository holds the downloads: the module's firmware and the command-line program (`vtx-guardian-hd-cli`) that updates the module and sets its behaviour. Both work through the UART the module already has: no programmer is needed.

## Downloads

Everything is under [Releases](https://github.com/ericogr/vtx-guardian-hd-release/releases)..

| Release | Files |
| :--- | :--- |
| `cli-v<version>` | `vtx-guardian-hd-cli-<platform>` (with `.exe` on Windows) and `SHA256SUMS`. Platforms: `linux-x86_64`, `linux-aarch64`, `macos-x86_64`, `macos-aarch64`, `windows-x86_64`, `windows-aarch64` |
| `firmware-v<version>` | `firmware.zip`, with the update file `vtx-guardian-hd-<hardware revision>.vgfw` (today `vtx-guardian-hd-ch32v003j4m6.vgfw`) |

Check a download against `SHA256SUMS` (`sha256sum -c SHA256SUMS --ignore-missing` on Linux). On Linux and macOS make the program executable with `chmod +x`. The examples below call it `vtx-guardian-hd-cli`: rename it, or put it in your `PATH`. The program is not code-signed, so Windows and macOS may ask you to confirm before running it.

## Connecting

- **Behind INAV (the usual way):** the flight controller on USB, and the module on the UART of the flight controller that has the **MSP-OSD** function (with its MSP baud at 115200). The CLI checks that INAV is not armed, reads its UARTs and puts INAV in serial passthrough towards the module.
- **Directly:** a USB-serial adapter at 3.3 V logic levels: its TX to pin 3 of **IN**, its RX to pin 4 and GND to pin 2.

In both cases the module must be powered from the battery, with the **VTX off** (press the button if it is on): with the VTX on the module ignores the CLI.

## Commands

```bash
vtx-guardian-hd-cli ports                              # the USB serial ports
vtx-guardian-hd-cli info                               # what the module is: chip uid, versions, settings, battery
vtx-guardian-hd-cli update <firmware.vgfw>             # update the firmware
vtx-guardian-hd-cli config list [--advanced]           # every setting: value, default and what it takes
vtx-guardian-hd-cli config get <name>
vtx-guardian-hd-cli config set <name>=<value>...       # for example: disarm-delay=10 cells=4 led-code=off
vtx-guardian-hd-cli config reset <name>... | --all     # back to the defaults
vtx-guardian-hd-cli config calibrate --voltage 16.8    # the module learns what your multimeter reads
```

All of them except `ports` take these options:

| Option | Does |
| :--- | :--- |
| `--port <PORT>` | The serial port of the module or of the flight controller. Without it, the only USB serial port is used; with several (or none) the program says so and stops. `ports` lists them |
| `--uart <N>` | The INAV UART wired to the module. Without it, the only UART with the MSP-OSD function is used |
| `-v`, `--verbose` | Shows what is tried and what comes of it |
| `--keep-passthrough` | Leaves INAV in serial passthrough at the end. By default the CLI takes INAV out of it, which restarts the flight controller. INAV then stays there until it is power cycled |

`--help` on any command lists the same. `info` and `config` do not change the firmware; `config list` prints the name, range and default of every setting.

**Settings** need the module running its firmware, with the VTX off. The new values are stored at once and **apply at the module's next power-up**: unplug the battery and plug it in again. A setting can be given by number or by its word (`never`, `off`, `on`, `auto`, `v1`, `v2`), and the program refuses a value the module would refuse before sending it. `config calibrate` needs the module powered from the battery being measured, with the multimeter on the battery leads.

## Update the firmware

The update keeps the settings.

1. Download `firmware.zip` from the latest `firmware-v*` release and take out the `.vgfw` for your hardware revision. Give it to the CLI as it is: the CLI takes only `.vgfw` files.
2. Connect the flight controller to the computer by USB, connect the battery, take the propellers off, and make sure the **VTX is off**.
3. Run:

   ```bash
   vtx-guardian-hd-cli update vtx-guardian-hd-ch32v003j4m6.vgfw
   ```

   Add `-v` to see each step.

The CLI finds the module (behind INAV it first checks that INAV is not armed and puts it in serial passthrough), checks that the file is for your hardware revision before anything is erased, restarts the module into its bootloader, writes and checks the new firmware, starts it and waits until it confirms. It then takes INAV out of passthrough, which restarts the flight controller: its USB port goes away for a few seconds and comes back, perhaps under another name. The last line shows the firmware before and after.

If the update is cut short (the battery unplugged, for example), the module waits in its bootloader and does not start the old firmware: run the update again.

## Problems

| What you see | What to do |
| :--- | :--- |
| `nothing answers on this port` | Check the wires, that the battery is connected and that the VTX is off. If an earlier attempt left INAV in its CLI, power-cycle the flight controller |
| `the flight controller is armed` | Disarm it and take the propellers off. The CLI does not start the passthrough on an armed INAV |
| `no UART of the flight controller has the MSP-OSD function` | Enable MSP-OSD on the UART wired to the module (INAV, Ports tab), or give it with `--uart` |
| `INAV runs the MSP of UART N at baud index ..., not at 115200` | In INAV's Ports tab, set the MSP baud of that UART to 115200 |
| `INAV did not leave the serial passthrough` | Power-cycle the flight controller |
| `the module did not restart into the bootloader` | The VTX is on. Switch it off with the button and run the update again |
| `this is not a firmware file of the module (a .vgfw)` | You gave a plain `.bin`. Use the `.vgfw` of the release |
| `the image carries the compatibility key ... another hardware revision` | The file is not for your hardware revision |
| A new setting does not seem to apply | Settings apply at the next power-up: unplug the battery and plug it in again |

## License

Copyright (c) 2026 EricoGR. All rights reserved: see [LICENSE](LICENSE). The licenses of the third-party components in the files of the releases are in [THIRD_PARTY.md](THIRD_PARTY.md).
