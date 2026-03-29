# MeshGrid-Node-Firmware

Official firmware distribution and installation guide for **MeshGrid-Node**.  
This repository provides official firmware releases and flashing instructions for supported ESP32-based boards **and the required LoRa radio hardware** used in the MeshGrid ecosystem.

MeshGrid-Node is designed to enable **encrypted 868 MHz LoRa mesh communication** and support **offline coordination and tactical mapping workflows** for small teams and field-oriented use cases.

> **Important:** The current firmware build is intended to run on supported ESP32 boards **together with the EBYTE E22-900T22D LoRa module**. The LoRa radio is a required hardware component for MeshGrid-Node operation and is not optional.

---

## Table of Contents

- [Overview](#overview)
- [Supported Hardware](#supported-hardware)
  - [Supported Boards](#supported-boards)
  - [Required LoRa Module](#required-lora-module)
- [Release Scope](#release-scope)
- [Firmware Installation](#firmware-installation)
  - [Requirements](#requirements)
  - [Recommended Flashing Method](#recommended-flashing-method)
  - [Connecting the Board](#connecting-the-board)
  - [Finding the Serial Port](#finding-the-serial-port)
  - [Flashing the Firmware](#flashing-the-firmware)
  - [Manual Bootloader Mode](#manual-bootloader-mode)
  - [Optional: Erase Flash Before Installation](#optional-erase-flash-before-installation)
  - [Verifying the Installation](#verifying-the-installation)
- [Troubleshooting](#troubleshooting)
- [Important Notes](#important-notes)
- [Release Usage Notice](#release-usage-notice)
- [License](#license)

---

## Overview

MeshGrid-Node-Firmware is the official firmware distribution repository for MeshGrid-compatible nodes.

The firmware is intended to provide a lightweight, encrypted LoRa mesh communication layer for offline operation in constrained environments. The primary focus is practical field deployment on supported ESP32 hardware using prebuilt firmware binaries distributed through GitHub Releases.

This repository is intended for:

- Official firmware distribution
- Firmware installation instructions
- Release-specific binary information
- End-user flashing guidance for supported hardware

> MeshGrid-Node is **not an ESP32-only firmware target**. The firmware is built for a node configuration that includes both a supported ESP32 board and the **EBYTE E22-900T22D** LoRa radio module.

---

## Supported Hardware

### Supported Boards

The current firmware release is intended for the following ESP32-based boards:

- **ESP32-S**
- **ESP32 DevKit V1**

### Required LoRa Module

The current MeshGrid-Node firmware build expects the following LoRa radio module in the target hardware:

- **EBYTE E22-900T22D**

The EBYTE E22-900T22D is a required part of the supported MeshGrid-Node hardware configuration for this firmware release.

Important compatibility notes:

- A supported ESP32 board alone is **not sufficient**
- The firmware expects the node to include the **EBYTE E22-900T22D** module
- The prebuilt binary is intended for the expected MeshGrid-Node wiring and hardware layout
- If a different LoRa module, different pin mapping, or a different radio wiring layout is used, the firmware may flash successfully but LoRa communication may fail or behave unpredictably
- Always verify that the selected firmware binary matches the target board, radio module, hardware revision, and expected MeshGrid-Node wiring configuration

---

## Release Scope

This repository distributes **prebuilt firmware binaries** in `.bin` format.

Depending on the release, firmware may be provided in one of the following formats:

1. **Single merged image**
   - One `.bin` file containing the full flash image

2. **Multi-file firmware package**
   - Separate files such as:
     - `bootloader.bin`
     - `partitions.bin`
     - `boot_app0.bin`
     - `firmware.bin`

These releases are intended for supported ESP32 boards used with the **EBYTE E22-900T22D** LoRa module in the expected MeshGrid-Node hardware configuration.

Always check the corresponding GitHub Release page for the exact binary layout, compatibility notes, offsets, and any release-specific instructions.

---

## Firmware Installation

This section explains how to flash the released `.bin` firmware file to a supported ESP32 board.

### Requirements

#### Hardware

- 1 × **ESP32-S** or **ESP32 DevKit V1**
- 1 × **EBYTE E22-900T22D** LoRa module
- 1 × USB data cable
- A computer running Windows, macOS, or Linux

#### Software

- Python 3
- `esptool`

#### USB Drivers

Depending on the USB-to-serial chip used by your ESP32 board, you may need one of the following drivers:

- **CP210x**
- **CH340**

If the board does not appear as a serial device after connecting it, install the correct driver before continuing.

> Flashing the firmware installs the software on the ESP32. For the node to provide LoRa functionality after boot, the **EBYTE E22-900T22D** must also be present and connected correctly according to the intended MeshGrid-Node hardware design.

---

## Recommended Flashing Method

The recommended and most reliable method for installing MeshGrid firmware is **esptool**.

### 1. Install Python

Check whether Python 3 is already installed:

```bash
python --version
```

or:

```bash
python3 --version
```

### 2. Install esptool

Install `esptool` using pip:

```bash
pip install esptool
```

If your system uses `pip3`, run:

```bash
pip3 install esptool
```

You can confirm the installation with:

```bash
esptool.py version
```

---

## Connecting the Board

Connect the ESP32 board to your computer using a **USB data cable**.

> Some USB cables only provide power and do not support data transfer. If the board is not detected, try another cable before troubleshooting further.

> **Important:** The USB cable is used to flash the ESP32. The complete MeshGrid-Node hardware is expected to include the **EBYTE E22-900T22D** module connected to the ESP32 with the correct wiring required by the target build.

---

## Finding the Serial Port

### Windows

Open **Device Manager** and check under:

- **Ports (COM & LPT)**

Typical examples:

- `COM3`
- `COM5`

### macOS

Run:

```bash
ls /dev/cu.*
```

Typical examples:

- `/dev/cu.usbserial-0001`
- `/dev/cu.SLAB_USBtoUART`

### Linux

Run:

```bash
ls /dev/ttyUSB* /dev/ttyACM*
```

Typical examples:

- `/dev/ttyUSB0`
- `/dev/ttyACM0`

---

## Flashing the Firmware

There are two common firmware release formats.

### Option A — Single Merged Firmware `.bin`

If the release contains **one merged firmware file**, flash it at address `0x0000`:

```bash
esptool.py --chip esp32 --port <PORT> --baud 460800 write_flash -z 0x0000 MeshGrid-Node-firmware.bin
```

#### Example (Windows)

```bash
esptool.py --chip esp32 --port COM5 --baud 460800 write_flash -z 0x0000 MeshGrid-Node-firmware.bin
```

#### Example (macOS / Linux)

```bash
esptool.py --chip esp32 --port /dev/cu.SLAB_USBtoUART --baud 460800 write_flash -z 0x0000 MeshGrid-Node-firmware.bin
```

---

### Option B — Multiple `.bin` Files

If the release contains separate files such as:

- `bootloader.bin`
- `partitions.bin`
- `boot_app0.bin`
- `firmware.bin`

then they must be flashed using the correct offsets.

Typical ESP32 flash layout:

```bash
esptool.py --chip esp32 --port <PORT> --baud 460800 write_flash -z \
0x1000 bootloader.bin \
0x8000 partitions.bin \
0xe000 boot_app0.bin \
0x10000 firmware.bin
```

> Use the exact offsets provided in the release notes if they differ from the example above. The release page is the source of truth for that build.

---

## Manual Bootloader Mode

Some ESP32 boards do not automatically enter flashing mode.

If flashing fails, use manual bootloader mode:

1. Press and hold the **BOOT** button
2. Press and release the **EN** button
3. Keep holding **BOOT** for 2–3 seconds
4. Start the flashing command
5. Release **BOOT** once the upload begins

This is commonly required when `esptool` cannot establish a connection with the board.

---

## Optional: Erase Flash Before Installation

If you are upgrading from an older build and encounter boot issues, corrupted settings, or unexpected behavior, erase the flash first:

```bash
esptool.py --chip esp32 --port <PORT> erase_flash
```

Then run the flashing command again.

> Erasing flash will remove saved configuration, pairing information, stored settings, and any previous firmware data.

---

## Verifying the Installation

After flashing completes successfully:

1. Press the **EN** button once to reboot the board
2. Open a serial monitor using the baud rate expected by the firmware
3. Confirm that the board boots normally and outputs startup logs

### Verification checklist

- The board powers on correctly
- The firmware starts without entering a boot loop
- No immediate crash or reset occurs
- Mesh or Bluetooth services become available if enabled by the build
- The board behaves consistently after a reboot

> A successful flash only confirms that the ESP32 firmware was written correctly. Full MeshGrid-Node operation also depends on the **EBYTE E22-900T22D** being installed and wired correctly.

---

## Troubleshooting

### Board is not detected

Possible causes:

- Faulty or power-only USB cable
- Missing CP210x or CH340 driver
- Bad USB port
- Unstable USB connection
- Board is not powered correctly

Recommended actions:

- Try a different USB cable
- Try another USB port
- Install the correct USB driver
- Reconnect the board
- Reboot the computer if the serial interface remains unavailable

---

### Permission denied on macOS or Linux

The current user may not have permission to access the serial device.

Try:

- Running the command with elevated permissions
- Adding your user to the correct serial-access group
- Reconnecting the board after permission changes

---

### esptool cannot connect to the board

If you see errors such as:

- `Failed to connect`
- `Timed out waiting for packet header`

the board most likely did not enter bootloader mode.

Use the steps in [Manual Bootloader Mode](#manual-bootloader-mode) and try again.

---

### Flashing succeeds but the board does not boot

Possible causes:

- Wrong firmware file
- Wrong flash offsets
- Incompatible board target
- Incomplete or incorrect release package
- Old flash contents conflicting with the new build

Recommended fix:

1. Erase flash
2. Reflash using the correct command
3. Confirm that the selected binary matches the target board
4. Re-check the release page for the correct package layout and offsets

---

### Firmware boots but LoRa communication does not work

Possible causes:

- The **EBYTE E22-900T22D** module is missing
- The LoRa module is not powered correctly
- The wiring between the ESP32 and E22-900T22D is wrong or incomplete
- The board and radio module do not match the expected MeshGrid-Node hardware layout
- The build was flashed to unsupported hardware
- The firmware expects a different pin mapping or wiring configuration

Recommended actions:

1. Confirm that the node includes the **EBYTE E22-900T22D**
2. Re-check the intended MeshGrid-Node wiring
3. Confirm that the selected firmware release supports your exact board and hardware revision
4. Verify that the radio module is connected exactly as expected by the target build
5. Re-read the release notes for hardware compatibility and known limitations

---

### Serial output is missing after flashing

Possible causes:

- Wrong serial monitor baud rate
- Bad USB cable or unstable port
- Firmware did not boot correctly
- Wrong firmware image flashed to the board

Recommended actions:

- Reconnect the board
- Press **EN** to reset
- Confirm the correct serial port is selected
- Reflash after erasing the chip if needed

---

## Important Notes

- The current MeshGrid-Node firmware build requires a supported ESP32 board **and** the **EBYTE E22-900T22D** LoRa module
- Only use official firmware binaries published in this repository or official MeshGrid release channels
- Flashing unofficial or modified binaries may cause instability, incompatibility, or loss of expected functionality
- Always verify that the firmware release matches your hardware revision before installation
- Do not assume all releases use the same binary layout or flash offsets
- Do not assume the firmware supports different LoRa modules unless the release notes explicitly say so
- Read the corresponding GitHub Release notes before flashing any new version

---

## Release Usage Notice

This repository is intended to distribute official MeshGrid-Node firmware builds and installation instructions only.

End users should follow the published release documentation carefully and avoid flashing firmware to unsupported hardware configurations.

For release-specific details such as:

- binary type
- flash offsets
- supported boards
- required radio hardware
- feature availability
- known limitations
- compatibility notes

always refer to the corresponding GitHub Release page.

---

## License

This firmware is distributed for **personal and non-commercial use only**.  
Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

### Restrictions

- ❌ Commercial redistribution is prohibited
- ❌ Selling hardware pre-loaded with this firmware is prohibited
- ❌ Reverse engineering is strictly prohibited

By downloading, flashing, or using this firmware, you agree to comply with the license terms and distribution restrictions described above.
