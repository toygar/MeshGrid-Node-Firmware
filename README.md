# MeshGrid-Node-Firmware

Official firmware distribution and installation guide for **MeshGrid-Node**.  
This repository provides official firmware releases and flashing instructions for supported ESP32-based boards **and the required LoRa radio hardware** used in the MeshGrid ecosystem.

MeshGrid-Node is designed to enable **encrypted sub-GHz LoRa mesh communication** and support **offline coordination and tactical mapping workflows** for small teams and field-oriented use cases.

> **Important:** The current firmware build is intended to run on supported ESP32 boards **together with the EBYTE E22-900T22D LoRa module and a compatible antenna**. The LoRa radio and antenna are required hardware components for MeshGrid-Node operation and are not optional.

> **Important:** This firmware is intended to communicate **only with the official MeshGrid mobile application** distributed through the **Apple App Store** and **Google Play Store**. Compatibility with third-party applications, unofficial clients, or alternative software is not guaranteed and is not part of the supported use case.

---

## Table of Contents

- [Overview](#overview)
- [Official Mobile App Compatibility](#official-mobile-app-compatibility)
- [BLE Pairing and Access Control](#ble-pairing-and-access-control)
- [Supported Hardware](#supported-hardware)
  - [Supported Boards](#supported-boards)
  - [Required LoRa Module](#required-lora-module)
  - [Required Antenna](#required-antenna)
- [Radio Compliance and User Responsibility](#radio-compliance-and-user-responsibility)
- [Release Scope](#release-scope)
- [Current Build Characteristics](#current-build-characteristics)
- [Firmware Installation](#firmware-installation)
  - [Requirements](#requirements)
  - [Recommended Flashing Method](#recommended-flashing-method)
  - [Connecting the Board](#connecting-the-board)
  - [Finding the Serial Port](#finding-the-serial-port)
  - [Flashing the Firmware](#flashing-the-firmware)
  - [Manual Bootloader Mode](#manual-bootloader-mode)
  - [First Boot and Provisioning](#first-boot-and-provisioning)
  - [Optional: Erase Flash Before Installation](#optional-erase-flash-before-installation)
  - [Verifying the Installation](#verifying-the-installation)
- [Getting Started After Installation](#getting-started-after-installation)
- [Serial Diagnostics and Maintenance](#serial-diagnostics-and-maintenance)
- [Troubleshooting](#troubleshooting)
- [Important Notes](#important-notes)
- [Release Usage Notice](#release-usage-notice)
- [Expected Wiring](#expected-wiring)
  - [Current Build Pin Map](#current-build-pin-map)
  - [Power and Signal Notes](#power-and-signal-notes)
- [AT Mode Configuration Warning](#at-mode-configuration-warning)
- [No Warranty / Compliance Disclaimer](#no-warranty--compliance-disclaimer)
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

> MeshGrid-Node is **not an ESP32-only firmware target**. The firmware is built for a node configuration that includes a supported ESP32 board, the **EBYTE E22-900T22D** LoRa radio module, and a **compatible antenna**.

---

## Official Mobile App Compatibility

This firmware is designed for use with the **official MeshGrid mobile application** only.

Supported client distribution channels:

- **Apple App Store** — official MeshGrid iOS application
- **Google Play Store** — official MeshGrid Android application

Official store links will be published here when available:

- **App Store:** `<ADD_APP_STORE_LINK_HERE>`
- **Google Play:** `<ADD_GOOGLE_PLAY_LINK_HERE>`

Important compatibility notes:

- This firmware is intended to communicate only with the official MeshGrid app
- Compatibility with third-party apps, custom clients, community tools, reverse-engineered software, or unofficial integrations is not guaranteed
- Unsupported software may fail to pair, fail to exchange data correctly, or behave unpredictably
- Protocol behavior, pairing logic, feature availability, and message handling are defined by the supported MeshGrid ecosystem only

After completing firmware installation and hardware setup, users can install the official MeshGrid app on a supported mobile device and use it to interact with MeshGrid nodes.

Once two supported nodes are installed, configured, and operating correctly, communication between two nodes can be established through the supported MeshGrid workflow in the official mobile app.


## BLE Pairing and Access Control

The current firmware build does **not** expose an open BLE transport. The BLE link is part of the supported MeshGrid workflow and is protected by pairing and link authentication.

Current behavior observed in the firmware:

- BLE advertising starts automatically on boot using the stored device name
- The device uses **BLE Secure Connections with MITM protection and bonding**
- A **6-digit static passkey** is derived from the provisioned mesh key and is required during pairing
- If the node is not provisioned with a valid key, the mesh radio remains disabled until provisioning is completed
- Unauthenticated BLE clients are disconnected automatically after the pairing timeout window

Operational notes:

- If the mesh password or provisioning state changes, previously bonded phones may need to be forgotten and paired again
- BLE connectivity alone does not imply that LoRa transport is active; provisioning, key state, and hardware readiness still matter
- This build is intended to be used through the official MeshGrid mobile application rather than generic BLE tools

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

### Required Antenna

A **compatible antenna** is also required for operation with the **EBYTE E22-900T22D**.

Important antenna notes:

- The radio module must **not** be treated as a complete RF solution on its own
- Operating the module without a suitable antenna may result in poor performance, unstable operation, or hardware risk
- The antenna used must be appropriate for the selected operating band and the intended regional deployment
- The user is responsible for selecting an antenna that is electrically and regulatorily appropriate for the configured frequency band, installation conditions, and applicable legal requirements

---

## Radio Compliance and User Responsibility

Radio devices operating in sub-GHz bands are subject to **regional, national, and local RF regulations**. These rules vary by country and jurisdiction and may impose requirements or restrictions on radio configuration and operation.

Such requirements may include, but are not limited to:

- allowed frequency bands
- channel or center frequency selection
- transmit power limits
- bandwidth and occupied spectrum limits
- duty-cycle or channel access restrictions
- modulation-related settings
- antenna-related limitations
- certification, registration, or other legal obligations where applicable

> **Legal Notice:** The **EBYTE E22-900T22D** is a configurable radio module. If the module is operated in **AT configuration mode** or by any other configurable interface, the selection and application of region-specific RF parameters are the **sole responsibility of the user**.

This includes, where applicable:

- selecting whether the device is configured for a locally permitted **868 MHz**, **900 MHz**, or other allowed regional operating range
- ensuring that configured radio parameters comply with the laws and regulations of the country or region where the device is used
- ensuring that the selected antenna and resulting RF behavior remain compliant with local legal requirements

The firmware distributor, repository maintainer, and release publisher do **not** guarantee that any default or user-selected radio parameters are lawful in every country, territory, or deployment environment.

Users must independently verify and comply with all applicable laws, regulations, licensing rules, and technical limits before operating the device.

If you are unsure whether a given configuration is legal in your location, **do not transmit** until the configuration has been reviewed against the applicable rules for that jurisdiction.

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

These releases are intended for supported ESP32 boards used with the **EBYTE E22-900T22D** LoRa module and a **compatible antenna** in the expected MeshGrid-Node hardware configuration.

Always check the corresponding GitHub Release page for the exact binary layout, compatibility notes, offsets, and any release-specific instructions.


## Current Build Characteristics

Based on the current `.ino` implementation, the present firmware build has the following operational characteristics that are important for installation, field behavior, and expectations:

- The implementation is **MeshLink v2 only**; older v1-style framing is not part of the supported wire format for this build
- The RF/BLE outer mesh header is a **fixed 16-byte header**
- The current application-level logical payload limit is **20 bytes** per mesh packet payload
- The maximum mesh wire body budget is **52 bytes** and the maximum full wire size is **68 bytes**
- LoRa encryption is enabled only when a valid provisioned key exists in NVS; otherwise the node is considered insecure or blocked depending on build policy
- **ACK tracking is used for unicast traffic**; broadcast message ACKs are disabled in the current build
- Relay behavior is enabled with a current **maximum hop count of 4**
- RF duplicate suppression is enabled with a **3-second deduplication window**
- Pending outgoing mesh packets are staged in NVS and replayed after reboot; the current staged packet limit is **5**
- The firmware clears the persisted pending packet queue automatically when the stored build number changes across firmware versions
- GPS transmission logic is enabled in the build, with a default broadcast interval of **30 seconds** when compatible GPS hardware is present

These items are not merely implementation details. They define practical behavior such as payload budgeting, interoperability expectations, delivery semantics, and what should or should not be assumed during deployment.

---

## Firmware Installation

This section explains how to flash the released `.bin` firmware file to a supported ESP32 board.

### Requirements

#### Hardware

- 1 × **ESP32-S** or **ESP32 DevKit V1**
- 1 × **EBYTE E22-900T22D** LoRa module
- 1 × **compatible antenna** for the selected operating band
- 1 × USB data cable
- A computer running Windows, macOS, or Linux

#### Software

- Python 3
- `esptool`
- A serial terminal or serial monitor capable of **115200 baud** for provisioning, verification, and diagnostics

#### USB Drivers

Depending on the USB-to-serial chip used by your ESP32 board, you may need one of the following drivers:

- **CP210x**
- **CH340**

If the board does not appear as a serial device after connecting it, install the correct driver before continuing.

> Flashing the firmware installs the software on the ESP32. For the node to provide LoRa functionality after boot, the **EBYTE E22-900T22D** and a **compatible antenna** must also be present and connected correctly according to the intended MeshGrid-Node hardware design.

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

> **Important:** The USB cable is used to flash the ESP32. The complete MeshGrid-Node hardware is expected to include the **EBYTE E22-900T22D** module and a **compatible antenna**, connected to the ESP32 with the correct wiring required by the target build.

> **Important:** The current firmware build expects the LoRa module UART to already match the firmware-side serial configuration. In the present `.ino` implementation, the radio UART is configured for **9600 baud, 8N1**. If the E22 module was previously changed to a different UART setting in AT/configuration mode, the firmware may boot normally while all LoRa communication silently fails.

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


## First Boot and Provisioning

The current firmware build is **not** purely plug-and-play after flashing. The `.ino` implementation shows that first boot behavior depends on whether a valid provisioned mesh key already exists in NVS.

What happens on boot:

- The USB serial console starts at **115200 baud**
- A persistent **random 16-bit node ID** is generated on first boot and stored in NVS
- If no device name is present, the node creates a default name in the form **`MeshGrid_XX`**
- If a valid key is not present and provisioning is required by the build policy, the node enters a provisioning loop and the mesh radio remains disabled until provisioning succeeds

### Provisioning inputs accepted over USB serial

The provisioning loop accepts line-based commands over the USB serial console:

- `NAME:ABC123`  
  Sets a custom short device name
- `ROSTER:101,102,103`  
  Stores an allow-list / roster in NVS
- `ROSTER:CLEAR` or `ROSTER:OFF`  
  Disables the stored roster
- `<password>`  
  Any password line between **8 and 64 characters** provisions the node key material

### Provisioning behavior and constraints

- The password is transformed into key material and stored in NVS; nodes must use the **same password** to participate in the same private mesh
- Custom short names are limited to **1-6 characters** and are restricted to **A-Z** and **0-9**
- While waiting for provisioning, the onboard LED blinks repeatedly
- After provisioning is successfully applied, the onboard LED blinks **five times quickly** as confirmation
- If stored key metadata no longer matches the firmware policy/KDF version, the node requires reprovisioning

This point is operationally critical: **a successful flash is not equivalent to a usable radio node**. Without a valid provisioned key, the current secure-mode build can remain unable to participate in the mesh.


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

> A successful flash only confirms that the ESP32 firmware was written correctly. Full MeshGrid-Node operation also depends on the **EBYTE E22-900T22D** and a **compatible antenna** being installed and wired correctly.

---

## Getting Started After Installation

After the firmware and hardware setup are complete:

1. Verify that both nodes are powered correctly and boot without error
2. Verify that the **EBYTE E22-900T22D** module and compatible antenna are installed on each node
3. Install the official **MeshGrid** mobile app from the **Apple App Store** or **Google Play Store**
4. Open the official app and follow the supported MeshGrid setup flow
5. Add or connect the nodes as supported by the application
6. Once two supported nodes are installed and configured correctly, communication between the two nodes can be established through the official MeshGrid mobile application

> The supported user workflow is based on the official MeshGrid application. Firmware-only installation does not by itself provide the complete supported user experience.


## Serial Diagnostics and Maintenance

The current firmware build exposes useful runtime diagnostics through the USB serial console at **115200 baud**. These commands are valuable when verifying real hardware behavior rather than guessing from the mobile side.

Supported commands observed in the `.ino` implementation:

- `stats` — prints runtime counters for AUX waits, relay activity, BLE auth results, ACK results, queue pressure, power profile, and radio sanity checks
- `sched` — prints scheduler slot usage
- `ack` — prints pending ACK tracker usage
- `roster` — prints current roster / allow-list state
- `roster clear` or `roster off` — disables roster filtering
- `roster set 101,102,103` — updates the runtime roster
- `stack` or `tasks` — prints task stack high-water marks for `LoRaTask`, `BleTask`, and `GpsTask`
- `health` — prints a broader health snapshot including heap, stack, pending queues, and protocol counters
- `radio` — prints LoRa UART/radio sanity information and the active UART pin mapping
- `resetstats` — resets runtime counters without erasing protocol/NVS state

These commands should be considered part of the practical support surface for this firmware, especially during bring-up, field testing, and fault isolation.

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
- A compatible antenna is missing
- The LoRa module is not powered correctly
- The wiring between the ESP32 and E22-900T22D is wrong or incomplete
- The board and radio module do not match the expected MeshGrid-Node hardware layout
- The build was flashed to unsupported hardware
- The firmware expects a different pin mapping or wiring configuration
- The radio parameters configured for the deployment region are incorrect or non-compliant
- The node was flashed successfully but **was never provisioned with a valid mesh key**
- The E22 module UART settings do not match the firmware expectation of **9600 baud, 8N1**

Recommended actions:

1. Confirm that the node includes the **EBYTE E22-900T22D**
2. Confirm that a **compatible antenna** is installed
3. Re-check the intended MeshGrid-Node wiring
4. Confirm that the selected firmware release supports your exact board and hardware revision
5. Verify that the radio module is connected exactly as expected by the target build
6. Verify that the configured radio parameters are appropriate for the deployment region
7. Confirm from serial logs whether the node reports provisioning/key-policy problems
8. Confirm that the E22 UART configuration still matches the current firmware expectation
9. Re-read the release notes for hardware compatibility and known limitations

---

### The mobile app cannot communicate with the node

Possible causes:

- The firmware was not installed correctly
- The hardware does not match the supported MeshGrid-Node design
- The node wiring is incorrect
- The LoRa module or antenna is missing
- The user is attempting to use unsupported third-party software
- The node setup has not been completed through the official MeshGrid app workflow
- BLE pairing did not complete successfully
- The phone is holding an old BLE bond after reprovisioning or device identity changes

Recommended actions:

1. Confirm that the node uses supported hardware
2. Confirm that the firmware flash process completed successfully
3. Confirm that the official MeshGrid app is being used
4. Confirm that the node was added or configured according to the supported application workflow
5. If pairing fails repeatedly, remove the old BLE bond from the phone and pair again
6. Re-check hardware, power, wiring, and firmware compatibility

---


### Device stays in provisioning mode or reports secure-mode errors

Possible causes:

- No valid mesh key is stored in NVS
- Stored key metadata does not match the current firmware policy or KDF version
- Provisioning was interrupted or incomplete
- The serial monitor is using the wrong baud rate

Typical symptoms:

- Serial logs report provisioning or key-policy errors
- The node repeatedly waits for provisioning input
- The mesh radio never becomes operational even though flashing succeeded

Recommended actions:

1. Open the USB serial console at **115200 baud**
2. Re-run provisioning with a valid password line
3. Re-apply `NAME:` and `ROSTER:` settings if needed
4. If the firmware was upgraded across policy changes, reprovision the node instead of assuming old key material remains valid

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

- The current MeshGrid-Node firmware build requires a supported ESP32 board, the **EBYTE E22-900T22D** LoRa module, and a **compatible antenna**
- The current build is **MeshLink v2 only** and should not be assumed to interoperate with older or differently framed firmware variants
- If suitable battery capacity, current delivery, voltage stability, or overall power requirements are not met, message delivery issues, transmission instability, or other communication problems may occur during operation
- In the current build, a valid provisioned mesh key is operationally critical; flashing alone does not guarantee an active node
- The current firmware expects the E22 UART side to operate at **9600 baud, 8N1**
- The current logical mesh payload limit is **20 bytes**, so application-side payload budgeting is required
- Broadcast messaging does **not** use ACK tracking in the current build
- This firmware is intended to communicate only with the official **MeshGrid** mobile application distributed through the **App Store** and **Google Play Store**
- Compatibility with third-party applications, unofficial clients, or alternative software is not guaranteed
- Only use official firmware binaries published in this repository or official MeshGrid release channels
- Flashing unofficial or modified binaries may cause instability, incompatibility, or loss of expected functionality
- Always verify that the firmware release matches your hardware revision before installation
- Do not assume all releases use the same binary layout or flash offsets
- Do not assume the firmware supports different LoRa modules unless the release notes explicitly say so
- Do not assume any default radio configuration is legal in every country or region
- If the module is configured through **AT mode** or any configurable interface, lawful regional RF settings are the **user's responsibility**
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
- required antenna considerations
- mobile app compatibility scope
- feature availability
- known limitations
- compatibility notes

always refer to the corresponding GitHub Release page.

---

## Expected Wiring

The current firmware build is intended for a specific MeshGrid-Node hardware layout using:

- a supported **ESP32** board
- an **EBYTE E22-900T22D** LoRa module
- a **compatible antenna**

> **Important:** Flashing the firmware alone does not guarantee functional radio operation. Correct wiring between the ESP32, the EBYTE E22-900T22D, and the required antenna is mandatory.

Users must ensure that:

- the selected ESP32 board matches the intended target build
- the **EBYTE E22-900T22D** is physically present in the node
- the module is connected using the expected wiring and pin mapping for that hardware revision
- the radio module is supplied with an appropriate and stable power source
- a compatible antenna is connected before RF operation
- any auxiliary, mode-selection, UART, or control pins required by the hardware design are wired correctly

### Current Build Pin Map

Based on the current `.ino` implementation, the active pin map for the present build is:

#### LoRa / E22 UART

- **ESP32 GPIO16** ← E22 TX
- **ESP32 GPIO17** → E22 RX
- **ESP32 GPIO15** ← E22 AUX
- LoRa UART setting expected by firmware: **9600 baud, 8N1**

#### GPS UART

- **ESP32 GPIO25** ← GPS TX (NMEA input into ESP32)
- **ESP32 GPIO26** → GPS RX

#### LEDs / local indicators

- **ESP32 GPIO2** — onboard status LED
- **ESP32 GPIO27** — RGB LED Red
- **ESP32 GPIO32** — RGB LED Green
- **ESP32 GPIO33** — RGB LED Blue

#### Reserved outputs driven at boot by the current build

- **ESP32 GPIO21** — driven LOW during setup
- **ESP32 GPIO22** — driven LOW during setup

> The source also contains an alternative compile-time LoRa UART mapping for PSRAM-related board layouts, but the current build shown in the uploaded `.ino` uses **GPIO16/GPIO17** for the LoRa UART path. Do not assume a different pin map unless the release notes explicitly say so.

### Power and Signal Notes

- The firmware uses the E22 **AUX** pin as a module-ready / UART-idle signal before transmit
- AUX handling improves UART-side reliability, but it is **not** true RF carrier sensing and should not be treated as collision avoidance in the radio sense
- The current build does **not** drive E22 **M0/M1** configuration pins; if the module requires AT/configuration changes, that must be handled outside the normal runtime path
- GPS support is enabled in the current build, but the code can still report **no hardware** if a GPS receiver is not physically present or not producing valid NMEA data
- The radio module and ESP32 must share a stable power source and common ground; marginal power behavior will look like software instability even when the firmware is correct

If the hardware layout, pin mapping, or radio wiring differs from the intended MeshGrid-Node design, the firmware may:

- boot successfully but fail to communicate over LoRa
- behave unpredictably
- provide unstable or degraded RF performance
- fail to operate as intended

The user is responsible for verifying that the actual hardware assembly matches the intended MeshGrid-Node wiring configuration before operation.

---

## AT Mode Configuration Warning

The **EBYTE E22-900T22D** is a configurable radio module. Depending on the hardware setup and deployment requirements, the module may support parameter changes through **AT mode** or another configuration interface.

These configurable parameters may include, but are not limited to:

- operating frequency or channel settings
- air data rate
- UART baud rate
- transmit power
- packet-related radio parameters
- other region-sensitive RF settings

For the current firmware build, one point is especially important: the runtime code expects the E22 UART side to already match **9600 baud, 8N1**. A module configured to different UART parameters may appear powered and wired correctly while still remaining unusable to the firmware.

> **Warning:** Any configuration applied to the **EBYTE E22-900T22D** through **AT mode** or any other configuration method is performed entirely at the user's own responsibility.

The user is solely responsible for:

- selecting settings that are lawful in the country or region of operation
- ensuring that configured frequency ranges, channel plans, air rate, power level, and related RF parameters comply with applicable regulations
- ensuring that the selected antenna and resulting transmission characteristics remain compliant with local legal limits
- keeping the module UART configuration aligned with the firmware expectation when AT/configuration changes are made
- avoiding unlawful or non-compliant radio operation

The firmware distributor, repository maintainer, and release publisher do **not** certify, guarantee, or represent that any user-selected module configuration is lawful, compliant, or appropriate for any particular jurisdiction.

If there is any uncertainty regarding lawful radio settings, the device must **not** be placed into transmission service until the configuration has been verified by the user against the applicable regional requirements.

---

## No Warranty / Compliance Disclaimer

This repository and its firmware releases are provided on an **"as is"** basis, without warranties of any kind, whether express or implied.

To the maximum extent permitted by applicable law:

- no warranty is provided regarding fitness for a particular purpose
- no warranty is provided regarding uninterrupted or error-free operation
- no warranty is provided regarding compatibility with every hardware variant or regional RF environment
- no warranty is provided regarding legal compliance of any specific radio configuration, antenna selection, or deployment scenario

The firmware distributor, repository maintainer, and release publisher disclaim responsibility for:

- incorrect hardware assembly
- incorrect wiring
- incompatible antennas
- unsupported board or module combinations
- unlawful RF configuration
- regulatory violations
- damage, interference, malfunction, or loss resulting from misuse, misconfiguration, or non-compliant operation

Use of this firmware, the **EBYTE E22-900T22D** module, and any associated RF hardware is entirely at the user's own risk.

Users are solely responsible for:

- verifying hardware compatibility
- confirming correct wiring and safe assembly
- selecting lawful region-specific RF parameters
- ensuring antenna suitability
- complying with all applicable local, national, and regional laws, regulations, certifications, and operating restrictions

## License

This firmware is distributed for **personal and non-commercial use only**.  
Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

### Restrictions

- ❌ Commercial redistribution is prohibited
- ❌ Selling hardware pre-loaded with this firmware is prohibited
- ❌ Reverse engineering is strictly prohibited

By downloading, flashing, configuring, or using this firmware, you agree to comply with the license terms, distribution restrictions, and all applicable RF laws and regulations.

---
