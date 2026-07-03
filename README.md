<p align="center">
  <a href="https://meshgrid.org"><img src="docs/meshgrid-logo.png" alt="MeshGrid" width="240" height="240"></a>
</p>

## MeshGrid Node

![Firmware Distribution](https://img.shields.io/badge/Firmware-Distribution-1f6feb)
![ESP32](https://img.shields.io/badge/Target-ESP32-0f766e)
![LoRa](https://img.shields.io/badge/Radio-EBYTE%20E22--900T22D-7c3aed)
![Protocol](https://img.shields.io/badge/Protocol-MeshLink%20v2-2563eb)
![License](https://img.shields.io/badge/License-CC%20BY--NC--ND%204.0-111827)

**Tactical Mesh Networking & Coordination Infrastructure**  
Coordinate your team when the network fails.

### The problem

In field operations, connectivity is often **unreliable or absent**. Typical approaches stitch together **disconnected tools**-mapping software, mesh stacks, and device configuration-each from a different mindset. That creates **setup overhead**, **fragmented workflows**, and **delays** exactly when time matters most.

### The solution

**MeshGrid** is a **field-ready coordination system** built on **LoRa mesh** networking. It replaces complex, multi-vendor setups with **one coherent stack** focused on **immediate usability** rather than integration projects.

### What teams can do

- **Share a common offline map** for situational awareness.
- **Track team members** with timely position updates on the mesh.
- **Communicate over long range** with **no cellular or internet** infrastructure required.

### Why MeshGrid is different

- **Streamlined setup:** Fewer steps and less brittle configuration-get running in **seconds**, not a long integration cycle.
- **Integrated workflow:** Mapping, positioning, and messaging are **designed to work together**, not bolted on after the fact.
- **Operations-first:** Built for **team awareness** in the field-not generic chat as the primary metaphor.
- **Firmware built for stability:** The node runtime is tuned for **low resource use** and **predictable concurrent behavior** on embedded hardware.
- **Unified experience:** A **mobile interface** aligned with the **node hardware** for real-world use.

### System overview

MeshGrid combines two pillars:

1. **ESP32-based LoRa nodes** - Low-power, long-range **mesh** links between devices, with asynchronous task handling for **continuous** data flow.
2. **Official mobile app** - Connects to nodes over **Bluetooth** to manage mapping, precise positioning, and **team coordination** tools.

Together they form a **self-contained coordination layer** for teams working **without** traditional connectivity.

---

**This repository** is the **official distribution channel** for **MeshGrid-Node** firmware binaries, flashing instructions, and published hardware references (**[HARDWARE_PINOUT.html](HARDWARE_PINOUT.html)**, **[HARDWARE_SCHEMATIC.svg](HARDWARE_SCHEMATIC.svg)**). **Project website:** [https://meshgrid.org](https://meshgrid.org) · **MeshGrid app (iOS):** [App Store](https://apps.apple.com/tr/app/meshgrid/id6760400899)

MeshGrid-Node enables **encrypted sub-GHz LoRa mesh** communication and **offline coordination and tactical mapping** workflows for small teams and field-oriented use cases.

Communication between nodes and the official app uses **MeshLink v2**, a **MeshGrid-specific** protocol (see [About MeshLink v2](#about-meshlink-v2)). It is **not** a generic industry-wide radio standard like LoRaWAN; it defines how encrypted mesh payloads, identities, and delivery semantics are framed for this product.

## At a Glance

- **Platform:** ESP32-S / ESP32 DevKit V1
- **Radio:** EBYTE E22-900T22D + compatible antenna
- **Transport:** **MeshLink v2** - MeshGrid’s own sub-GHz mesh framing (encrypted LoRa + BLE companion path); see [About MeshLink v2](#about-meshlink-v2)
- **Security:** Password-derived master key (PBKDF2-HMAC-SHA256), HKDF-SHA256 subkeys, Ascon AEAD (LoRa), AES-GCM (BLE)
- **Companion App:** Official MeshGrid mobile app — [App Store (iOS)](https://apps.apple.com/tr/app/meshgrid/id6760400899)
- **Distribution Model:** Prebuilt firmware releases with flashing guidance
- **Official website:** [meshgrid.org](https://meshgrid.org)
- **Hardware docs (this repo):** [`HARDWARE_PINOUT.html`](HARDWARE_PINOUT.html) · [`HARDWARE_SCHEMATIC.svg`](HARDWARE_SCHEMATIC.svg) (English) - published here so installers do not need a private source repo; see [Hardware documentation](#hardware-documentation-pinout-and-schematic)

> **Important:** The current firmware build is intended to run on supported ESP32 boards **together with the EBYTE E22-900T22D LoRa module and a compatible antenna**. The LoRa radio and antenna are required hardware components for MeshGrid-Node operation and are not optional.

> **Important:** This firmware is intended to communicate **only with the official MeshGrid mobile application** distributed through the **Apple App Store** and **Google Play Store**. Compatibility with third-party applications, unofficial clients, or alternative software is not guaranteed and is not part of the supported use case.

---

## Table of Contents

- [MeshGrid Node](#meshgrid-node)
- [At a Glance](#at-a-glance)
- [Overview](#overview)
  - [Hardware documentation (pinout and schematic)](#hardware-documentation-pinout-and-schematic)
  - [Firmware source code](#firmware-source-code)
  - [Building from source](#building-from-source)
- [Technology Overview](#technology-overview)
  - [About MeshLink v2](#about-meshlink-v2)
  - [MeshLink v2 wire layout](#meshlink-v2-wire-layout)
  - [Packet types and addressing](#packet-types-and-addressing)
  - [Cryptographic derivations](#cryptographic-derivations)
  - [BLE GATT profile](#ble-gatt-profile)
  - [Runtime tasks, watchdog, and internal limits](#runtime-tasks-watchdog-and-internal-limits)
  - [Security model limitations](#security-model-limitations)
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
  - [Battery Voltage Sense and App Reporting](#battery-voltage-sense-and-app-reporting)
  - [Power and Signal Notes](#power-and-signal-notes)
- [AT Mode Configuration Warning](#at-mode-configuration-warning)
- [No Warranty / Compliance Disclaimer](#no-warranty--compliance-disclaimer)
- [License](#license)
- [Security Policy](SECURITY.md)

---

## Overview

This repository (**MeshGrid-Node-Firmware**) is the official distribution channel for **MeshGrid-Node** firmware binaries and related installation documentation. General product information is available at **[https://meshgrid.org](https://meshgrid.org)**.

The firmware is intended to provide a lightweight, encrypted LoRa mesh communication layer for offline operation in constrained environments. The primary focus is practical field deployment on supported ESP32 hardware using prebuilt firmware binaries distributed through GitHub Releases.

This repository is intended for:

- Official firmware distribution
- Firmware installation instructions
- Release-specific binary information
- End-user flashing guidance for supported hardware

**Security vulnerabilities:** Use the process in **[`SECURITY.md`](SECURITY.md)** (including the security contact given there). Public GitHub Issues are **not** the correct channel for the **initial** report of a suspected vulnerability.

> MeshGrid-Node is **not an ESP32-only firmware target**. The firmware is built for a node configuration that includes a supported ESP32 board, the **EBYTE E22-900T22D** LoRa radio module, and a **compatible antenna**.

### Hardware documentation (pinout and schematic)

**This repository is the canonical, public place for wiring documentation.** Because **MeshGrid-Node-Firmware** is what you ship to users (and the firmware source tree may stay private), pinout and connection diagrams are kept **here**, next to the README and releases - not only inside an unpublished source repo.

| File | Purpose |
|------|---------|
| **[`HARDWARE_PINOUT.html`](HARDWARE_PINOUT.html)** | GPIO tables, UART speeds (USB / LoRa / GPS), E22 and GPS wiring summary, **10 kΩ + 10 kΩ** battery divider for **GPIO34**, BLE UUIDs, text wiring list. Open in any browser; suitable for print/PDF. |
| **[`HARDWARE_SCHEMATIC.svg`](HARDWARE_SCHEMATIC.svg)** | Block diagram: ESP32, **E22-900T22D**, GPS, cell + divider to **GPIO34**. Open in a browser or vector tool; printable. |

These files are **reference breadboard/wiring aids** (no official PCB). Always match the **pin map** to the firmware build you flashed ([Expected Wiring](#expected-wiring) in this README repeats the same defaults).

### Firmware source code

The Arduino / **firmware source** (`.ino` sketch, crypto, `provision.py`, `partitions.csv`, PlatformIO layout) is **not** published in **this** repository. This repo is intended for **released `.bin` files**, installation, compliance text, and the **hardware documents above**. If you maintain a separate private source tree, keep it in sync with what you ship.

### Building from source

Compiling firmware yourself requires that **internal source tree** (e.g. PlatformIO or Arduino IDE, partition scheme ~**1.9 MB** app on **4 MB** flash, provisioning tooling). That workflow is **out of scope** for this public README. **End users** flashing official binaries only need the **Releases** page of **this** repository and the **flash offsets** published with each build.

---

## Technology Overview

This section summarizes the architecture and protocol model used by the current firmware. Numeric build-specific limits are documented later in [Current Build Characteristics](#current-build-characteristics).

### About MeshLink v2

**MeshLink v2** is the **application-level mesh protocol** created **for the MeshGrid project** - it coordinates **MeshGrid-Node** firmware, the **EBYTE E22-900T22D** UART link, and the **official MeshGrid mobile application**. The specification (framing, packet types, security bindings, and delivery rules) is **defined and evolved inside the MeshGrid ecosystem**; it is **not** an independent IETF/IEEE/Bluetooth SIG standard, and it is **not** the same thing as **LoRaWAN** or other vendor-agnostic stacks. Third-party devices or apps should be assumed **incompatible** unless explicitly documented otherwise.

**What “v2” means:** the current generation uses a single **binary wire format** with a **fixed 16-byte header** and a strict **header + body** layout (documented under [MeshLink v2 wire layout](#meshlink-v2-wire-layout)). Earlier **v1**-style framing is **not** supported by this firmware; only **MeshLink v2** peers with aligned firmware/app versions are expected to interoperate.

MeshGrid-Node is built on an **ESP32 + EBYTE E22-900T22D** architecture and implements **MeshLink v2** as its transport for low-bandwidth, encrypted sub-GHz mesh communication. On the wire, the protocol uses that **fixed 16-byte frame header** with explicit sender/target IDs, hop count, packet type, message UID, and body length fields. The current implementation supports multiple packet classes including **messages, GPS data, shapes (including visibility/style updates), ACK frames, alerts, and local status events** (plus BLE-local control flows).

For security, provisioning collects a **user password** (see [First Boot and Provisioning](#first-boot-and-provisioning)); the firmware derives a **16-byte master key** with **PBKDF2-HMAC-SHA256** using salt **`MeshGrid/PasswordKdf/v1`** and **120,000** iterations (values fixed in firmware; may change if `PROV_KDF_VERSION` is bumped) and stores that master key in **NVS** - there are no embedded default mesh secrets. From the master key, the system derives separate material with **HKDF-SHA256**: a **BLE subkey** and a **LoRa subkey**, splitting cryptographic usage between the two transports. Per-packet LoRa keys may be derived from the LoRa subkey for Ascon. The **LoRa transport is protected with Ascon AEAD**, while the **BLE path uses AES-GCM** with the BLE-derived keys. This separation keeps the mobile-device link and the radio link isolated at the key level and allows the firmware to enforce a provisioning-first security model before mesh radio activity is enabled.

On the reliability side, the protocol includes **message UIDs, RF deduplication, replay tracking, bounded retransmission logic, and ACK-based delivery confirmation for unicast traffic**. ACK frames are correlated to the original message and validated against sender/target metadata before a packet is considered delivered. The firmware also applies **retry backoff and jitter** to reduce synchronized retransmissions. Broadcast traffic is intentionally treated differently and does **not** use the same ACK guarantee model. In practical terms, this provides a controlled reliability layer, but it is **not equivalent to full collision-free or guaranteed delivery under all RF conditions**.

Routing and forwarding are implemented as a lightweight **multi-hop flood/relay model** with a bounded hop limit. The node maintains **stable packet identity checks**, relay scheduling, and deferred transmission queues to prevent duplicate RF propagation from overwhelming the mesh. The firmware also includes **persistent pending-packet storage in NVS**, allowing selected outbound packets to survive reboot scenarios and be replayed after restart.

For hardware integration, the design uses **UART-based communication with the E22 radio**, optional **GPS over a dedicated UART**, optional **battery voltage sense on a divided ADC input (GPIO34)** for mobile-reported charge level, and a **BLE control/data bridge** for interaction with the official mobile application. The firmware also monitors module readiness through the **E22 AUX line** and uses a pseudo-busy transmit policy to reduce UART-side contention. This improves timing discipline around radio transmission, but it should be understood clearly: this is **not true RF carrier sensing or full CSMA/CA**.

At the application layer, the firmware currently supports **encrypted short-form messaging, node identity, GPS position broadcasting, geometry (circle, line, square) including delete/hide/show and style updates on the mesh where applicable, alerts (including delete), device status signaling, and mobile-app-driven provisioning/control flows**. Persistent node identity, provisioning metadata, device naming, replay state, and selected queue data are all managed through **ESP32 NVS storage**, making the node stateful across reboots while still remaining compact enough for embedded deployment.

### MeshLink v2 wire layout

The on-air and LoRa-facing frame is **`header || body`**, with **strict length**: total size **must** equal **16 + bodyLen** bytes (no shortened frames).

**16-byte header (little-endian where multi-byte):**

| Offset (bytes) | Content |
|----------------|---------|
| 0 | Magic **`0xA7`** |
| 1 | Wire version **`2`** (MeshLink v2) |
| 2–3 | Sender node ID (uint16 LE) |
| 4–5 | Target node ID (uint16 LE) |
| 6 | Hop count |
| 7 | [Packet type](#packet-types-and-addressing) (0–10) |
| 8–11 | Message UID (uint32 LE) |
| 12 | Body length (1–52) |
| 13 | Shape subtype or `0` (shape-related types) |
| 14–15 | Shape RGB565 color (uint16 LE) for applicable shape packets |

**Body:** either **plaintext** (when encryption is off) or **Ascon-128 AEAD** when LoRa encryption is on: **16-byte nonce** + ciphertext + **16-byte tag**, with the **16-byte header used as associated authenticated data (AAD)**. Maximum logical plaintext per packet is **20 bytes**; with encryption, the body budget is **52 bytes** maximum (hence **68 bytes** total wire including header).

### Packet types and addressing

**Broadcast / flood target ID:** **`0xFFFF`**. Unicast uses the peer’s **16-bit node ID** (stored in NVS, assigned at first boot).

| Type ID | Role |
|--------:|------|
| `0` | Message (chat / binary payload) |
| `1` | GPS position broadcast |
| `2` | Shape geometry (circle / line / square) |
| `3` | ACK (delivery confirmation) |
| `4` | Shape delete |
| `5` | Shape hide |
| `6` | Shape show |
| `7` | Status (mesh status channel; BLE may also use local extensions) |
| `8` | Shape style (fill/stroke metadata) |
| `9` | Alert |
| `10` | Alert delete |

Local-only BLE telemetry to the app (battery %, GPS summary, firmware build, etc.) is sent as **status** traffic using **local packet type `0xFE`** with per-feature **event IDs** (see [Battery Voltage Sense and App Reporting](#battery-voltage-sense-and-app-reporting)).

### Cryptographic derivations

- **Password → master key:** **PBKDF2-HMAC-SHA256**, salt **`MeshGrid/PasswordKdf/v1`**, **120,000** iterations → **16-byte** master key in NVS (see [First Boot and Provisioning](#first-boot-and-provisioning)). **Key-policy and KDF version numbers** are stored alongside the key; if they do not match the firmware’s expected **`PROV_KEY_POLICY_VERSION`** / **`PROV_KDF_VERSION`**, the node requires **reprovisioning** (no silent compatibility with stale metadata).
- **Master → transport keys:** **HKDF-SHA256** with `info` strings **`MeshGridBLEv2`** (BLE material) and **`MeshGridLoRav2`** (LoRa material).
- **BLE pairing PIN:** HKDF-SHA256 from the **BLE-derived** key with info **`MeshGridBLEPin/v1`**, then mapped to a **six-digit decimal** value in the range **100000–999999** (inclusive).
- **Per-packet LoRa keys:** For encrypted LoRa, a **packet key** is derived from the LoRa subkey using HKDF with a context that includes **sender ID** and **message UID**, so ciphertext is bound to those fields; the exact derivation is implemented in firmware (`meshDeriveLoraPacketKey`).

**BLE link encryption:** When enabled, inner payloads use **AES-128-GCM** (typical **12-byte nonce**, **16-byte tag**) on the BLE path; outer mesh framing still uses the same **16-byte MeshLink header** semantics as the LoRa side for application data.

### BLE GATT profile

The sketch exposes a **Nordic UART Service (NUS)–compatible UUID layout** for application data:

- **Service:** `6E400001-B5A3-F393-E0A9-E50E24DCCA9E`
- **RX characteristic** (central writes commands / mesh data to the node): `6E400002-B5A3-F393-E0A9-E50E24DCCA9E`
- **TX characteristic** (node notifies toward the phone): `6E400003-B5A3-F393-E0A9-E50E24DCCA9E`

Pairing uses **LE Secure Connections**, **MITM** (static passkey), and **bonding**; unauthenticated sessions are dropped after the [pairing timeout](#ble-pairing-and-access-control).

### Runtime tasks, watchdog, and internal limits

Typical layout in the published sketch (ESP32 **dual-core**):

- **BLE work** (notifications, pairing, decrypt/encrypt queues) and **LoRa mesh + scheduler + ACK/retry + RF dedup/relay** run as separate **FreeRTOS** tasks; **GPS NMEA** parsing runs when `ENABLE_GPS_TASK` is enabled.
- **Task watchdog** is configured for a **~15 second** timeout (reset on unrecoverable hang).
- Default **CPU frequency** for the power profile is **80 MHz** when idle; **Wi‑Fi is disabled** at boot.
- **Internal caps (order-of-magnitude; see firmware for exact names):** pending **ACK** slots **12**, transmit **scheduler** slots **24**, **pending direct** staging slots **4**, **replay** tracking for **24** sender contexts, persisted **pending TX** blob count **5** (with an internal ring up to **8** before NVS write coalescing), BLE notify **hold ring 24**. These prevent unbounded RAM use and flash wear.
- **Signal-test / probe airtime (BUILD 106+):** mesh chat probes use **8** stagger slots at **600 ms** spacing. With roster enabled, slot index follows **sorted roster node IDs** (plus self); without roster, callsign hash **`% 8`**. Flood duplicate broadcast TX is skipped when channel pressure **≥ 2**.

### Security model limitations

The mesh uses a **shared group password** (via PBKDF2) and **16-bit node IDs** chosen by each device. **Any node that knows the same mesh password can encrypt/decrypt traffic** and may **forge another node’s sender ID** on the radio unless stronger per-node identity (e.g. public-key or signed envelopes) is added in a future protocol. The optional **roster** limits which IDs the *firmware* will accept or target, but it is **not** a cryptographic proof of identity against an insider with the shared key. Treat deployment accordingly.

---

## Official Mobile App Compatibility

This firmware is designed for use with the **official MeshGrid mobile application** only.

Supported client distribution channels:

- **Apple App Store** - official MeshGrid iOS application
- **Google Play Store** - official MeshGrid Android application

Official store listings:

- **App Store (iOS):** [MeshGrid on the App Store](https://apps.apple.com/tr/app/meshgrid/id6760400899)
- **Google Play (Android):** *To be announced*

For **App Store promo codes**, email [info@meshgrid.org](mailto:info@meshgrid.org). **Only 100 promo codes** are available in total.

Important compatibility notes:

- This firmware is intended to communicate only with the official MeshGrid app
- Compatibility with third-party apps, custom clients, community tools, reverse-engineered software, or unofficial integrations is not guaranteed
- Unsupported software may fail to pair, fail to exchange data correctly, or behave unpredictably
- Protocol behavior, pairing logic, feature availability, and message handling are defined by the supported MeshGrid ecosystem only

After completing firmware installation and hardware setup, users can install the official MeshGrid app on a supported mobile device and use it to interact with MeshGrid nodes.

Once two supported nodes are installed, configured, and operating correctly, communication between two nodes can be established through the supported MeshGrid workflow in the official mobile app.


## BLE Pairing and Access Control

The current firmware build does **not** expose an open BLE transport. The BLE link is part of the supported MeshGrid workflow and is protected by pairing and link authentication. **Service and characteristic UUIDs** (NUS-compatible layout) are listed under [BLE GATT profile](#ble-gatt-profile).

Current behavior observed in the firmware:

- BLE advertising starts automatically on boot using the stored device name
- The device uses **BLE Secure Connections with MITM protection and bonding**
- A **6-digit static passkey** is derived (via HKDF-SHA256) from the **BLE subkey** produced after provisioning - i.e. from the same key hierarchy as the mesh, not an independent PIN - and is required during pairing
- If the node is not provisioned with a valid key, the mesh radio remains disabled until provisioning is completed
- Unauthenticated BLE clients are disconnected automatically after the pairing timeout window (currently **20 seconds**)

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

Download official binaries from the **[GitHub Releases](https://github.com/toygar/MeshGrid-Node-Firmware/releases)** page (not from a `releases/` folder in this repo). Always check the release notes for the exact binary layout, compatibility notes, flash offsets, and SHA256 checksums.


## Current Build Characteristics

Based on the current `.ino` implementation, the present firmware build has the following operational characteristics that are important for installation, field behavior, and expectations:

- The implementation is **MeshLink v2 only**; older v1-style framing is not part of the supported wire format for this build
- The RF/BLE outer mesh header is a **fixed 16-byte header**
- The current application-level logical payload limit is **20 bytes** per mesh packet payload
- The maximum mesh wire body budget is **52 bytes** and the maximum full wire size is **68 bytes**
- LoRa encryption is enabled only when a valid provisioned key exists in NVS; otherwise the node is considered insecure or blocked depending on build policy
- **ACK tracking is used for unicast traffic**; broadcast message ACKs are disabled in the current build
- Tracked unicast delivery currently uses an ACK timeout/retry model with jitter and up to **3 retransmissions** after the initial transmission
- Relay behavior is enabled with a current **maximum hop count of 4**
- RF duplicate suppression is enabled with a **3-second deduplication window**
- Pending outgoing mesh packets are staged in NVS and replayed after reboot; the current staged packet limit is **5**
- The optional node **roster / allow-list** (see provisioning `ROSTER:`) stores up to **8** node IDs
- **Internal queues (typical):** **12** concurrent **ACK** trackers, **24** **scheduler** TX slots, **4** **pending direct** slots, **24** **replay** sender slots (see [Runtime tasks, watchdog, and internal limits](#runtime-tasks-watchdog-and-internal-limits))
- The firmware clears the persisted pending packet queue automatically when the stored build number changes across firmware versions
- **Current public binary:** **BUILD 107** (`factory_v0.0.5.bin`) — see **[MeshGrid ESP32 - factory_v0.0.5 (BUILD 107)](https://github.com/toygar/MeshGrid-Node-Firmware/releases/tag/v0.0.5)** on GitHub Releases
- GPS UART (when hardware is present) defaults to **9600 baud, 8N1** on the documented pins; GPS transmission logic is enabled in the build with a default position broadcast interval of **30 seconds** when a compatible receiver is present and producing fixes
- **BLE telemetry cadence (when connected and authenticated):** local **GPS status** updates about every **3 seconds**; **battery** status about every **10 seconds** (see [Battery Voltage Sense and App Reporting](#battery-voltage-sense-and-app-reporting))
- **Battery monitoring** (when wired as documented): the node samples cell voltage on **GPIO34** through a **10kΩ + 10kΩ** divider, applies smoothing and stabilization in firmware, and sends **BLE status telemetry** to the official app about every **10 seconds** while connected - see [Battery Voltage Sense and App Reporting](#battery-voltage-sense-and-app-reporting)

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

### Option A - Single Merged Firmware `.bin`

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

### Option B - Multiple `.bin` Files

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
- A persistent **random 16-bit node ID** in **`1 … 65534`** (never **`0`**) is generated on first boot and stored in NVS
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

When a roster is **enabled**, the firmware applies **allow-list checks**: **inbound** packets are accepted from the roster (and always from **your own** node ID); **senders `0` or broadcast `0xFFFF`** do not pass the roster gate when the list is active. **Outbound** unicast to a peer requires that peer’s ID to be on the roster; **broadcast** and traffic to **self** remain allowed. Up to **8** IDs are stored.

**Node ID vs roster:** Each device gets a **random 16-bit node ID** on first boot (stored in NVS). It is **not** assigned by the user and **not** the hardware serial. The roster is **not** populated automatically — you must set the same allow-list on every node (`ROSTER:…`, `roster set …`, or an internal provisioning script). For **BUILD 106+** signal-test probe staggering, slot rank is the index of **your node ID** in the **sorted** roster list (not CSV entry order).

### Optional: provisioning helper script (`provision.py`)

If your internal firmware tree includes **`provision.py`**, it can drive the same USB serial protocol (password, optional `--name`, optional `--roster`) and is often more reliable than typing in a monitor. It is **not** part of this public repository. Close other serial programs before using it. Everyone else should use the [line-based provisioning](#provisioning-inputs-accepted-over-usb-serial) commands above.

### Provisioning behavior and constraints

- The password is run through **PBKDF2-HMAC-SHA256** with salt **`MeshGrid/PasswordKdf/v1`** and **120,000** iterations to produce the stored master key material in NVS; nodes must use the **same password** to participate in the same private mesh
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
3. Install the official **MeshGrid** mobile app from the [**App Store** (iOS)](https://apps.apple.com/tr/app/meshgrid/id6760400899). (Android: Google Play link under [Official Mobile App Compatibility](#official-mobile-app-compatibility) when available.)
4. Open the official app and follow the supported MeshGrid setup flow
5. Add or connect the nodes as supported by the application
6. Once two supported nodes are installed and configured correctly, communication between the two nodes can be established through the official MeshGrid mobile application

> The supported user workflow is based on the official MeshGrid application. Firmware-only installation does not by itself provide the complete supported user experience.


## Serial Diagnostics and Maintenance

The current firmware build exposes useful runtime diagnostics through the USB serial console at **115200 baud**. These commands are valuable when verifying real hardware behavior rather than guessing from the mobile side.

Supported commands observed in the `.ino` implementation:

- `stats` - prints runtime counters (including **`BUILD=`**), AUX waits, relay activity, BLE auth results, ACK results, queue pressure, power profile, and radio sanity checks
- `sched` - prints scheduler slot usage
- `ack` - prints pending ACK tracker usage
- `roster` - prints current roster / allow-list state
- `roster clear` or `roster off` - disables roster filtering
- `roster set 101,102,103` - updates the runtime roster
- `stack` or `tasks` - prints task stack high-water marks for `LoRaTask`, `BleTask`, and `GpsTask`
- `health` - prints a broader health snapshot including heap, stack, pending queues, and protocol counters
- `radio` - prints LoRa UART/radio sanity information and the active UART pin mapping
- `resetstats` - resets runtime counters without erasing protocol/NVS state
- `txprobe CALLSIGN SEQ` - enqueue one LoRa signal-test-style probe (bench / field validation; e.g. `txprobe ALPHA 42`)
- `stress on` / `stress on BRAVO1` / `stress off` - automatic probe every 5 seconds over LoRa (bench use only)

Unknown lines print a short hint: `try: stats sched ack roster stack tasks health radio resetstats txprobe stress`.

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
- Power instability or brownout during boot

Recommended fix:

1. Erase flash
2. Reflash using the correct command
3. Confirm that the selected binary matches the target board
4. Re-check the release page for the correct package layout and offsets
5. If resets happen immediately after boot, verify the power source and watch for brownout indications on the serial console

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
- **Cryptography (summary):** provisioning password → **PBKDF2-HMAC-SHA256** (salt `MeshGrid/PasswordKdf/v1`, **120,000** iterations) master key in NVS → **HKDF-SHA256** BLE/LoRa subkeys - details in [Cryptographic derivations](#cryptographic-derivations)
- **Shared mesh password** authenticates the *group*, not individual devices: a malicious participant with the same password may still **spoof another node’s ID** on the radio; see [Security model limitations](#security-model-limitations)
- The optional **`ROSTER:`** allow-list stores at most **8** node IDs
- The current build is **MeshLink v2 only** and should not be assumed to interoperate with older or differently framed firmware variants - see [About MeshLink v2](#about-meshlink-v2)
- If suitable battery capacity, current delivery, voltage stability, or overall power requirements are not met, message delivery issues, transmission instability, or other communication problems may occur during operation
- Battery **percentage** in the app is derived from **voltage** (with firmware smoothing), not coulomb counting; recalibrate or adjust hardware if readings disagree persistently with a multimeter - see [Battery Voltage Sense and App Reporting](#battery-voltage-sense-and-app-reporting)
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
- Report **security vulnerabilities** only through the process in **[`SECURITY.md`](SECURITY.md)**

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

For **security vulnerability reporting**, use **[`SECURITY.md`](SECURITY.md)**.

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
- GPS serial expected by firmware: **9600 baud, 8N1** (must match the connected module’s UART configuration)

#### LEDs / local indicators

- **ESP32 GPIO2** - onboard status LED
- **ESP32 GPIO27** - RGB LED Red
- **ESP32 GPIO32** - RGB LED Green
- **ESP32 GPIO33** - RGB LED Blue

#### Battery voltage sense (ADC)

- **ESP32 GPIO34** - **ADC1** input for **cell voltage** (read-only GPIO; must not exceed **~3.3 V** at the pin)
- Expected analog front-end: **two equal resistors** (typically **10 kΩ + 10 kΩ**) from **cell (+)** to **GND**, with the **midpoint** to GPIO34 - i.e. **Vpin ≈ Vcell / 2**. Firmware reconstructs **Vcell = Vpin × 2** (see source: `BATTERY_DIVIDER_RATIO_X100`).
- **Safety:** A fully charged **18650** can reach **~4.2 V**; the divider must keep the voltage at GPIO34 within the ESP32 ADC valid range (about **≤3.3 V**; with 10k/10k, **~2.1 V** max at the pin for a 4.2 V cell). **Never** connect the cell directly to a GPIO.

#### Reserved outputs driven at boot by the current build

- **ESP32 GPIO21** - driven LOW during setup
- **ESP32 GPIO22** - driven LOW during setup

> The source also contains an alternative compile-time LoRa UART mapping for PSRAM-related board layouts, but the current build shown in the uploaded `.ino` uses **GPIO16/GPIO17** for the LoRa UART path. Do not assume a different pin map unless the release notes explicitly say so.

### Battery Voltage Sense and App Reporting

When `ENABLE_BATTERY_MONITOR` is enabled in firmware (default in current sources), the node reports **remaining charge** as a **voltage-derived percentage** (a practical proxy, not a coulomb-counting fuel gauge).

**Hardware**

- **Divider:** 10 kΩ / 10 kΩ from cell (+) to GND, midpoint to **GPIO34**; **common ground** between the cell negative, ESP32 GND, and USB return path where applicable.

**Mapping (firmware)**

- Linear range: **3.35 V cell → 0% display**, **4.20 V cell → 100%** (`BATTERY_CELL_MV_EMPTY` / `BATTERY_CELL_MV_FULL`).
- **Calibration:** ESP32 ADC plus divider tolerance can read **tens to ~150 mV low** versus a multimeter on the cell. Firmware exposes **`BATTERY_ADC_CALIBRATION_OFFSET_MV`** (millivolts added to the reconstructed cell voltage before display/telemetry; default in recent builds is **+130 mV** - tune per board if needed).
- **Telemetry interval:** about **10 s** between local battery status notifications while BLE is connected and authenticated (`BLE_LOCAL_BATTERY_STATUS_INTERVAL_MS`).

**Stability behavior (factory `v0.0.5` / BUILD 107)**

- **Signal Test / sustained mesh probes (BUILD 102):** fixes a lockup after hundreds of probes when the official app runs Signal Test with BLE connected. BLE status/RF telemetry is shed under queue pressure; signal-test traffic uses a lighter path; stuck LoRa scheduler slots are reclaimed after 4.5 s. Validated with 420 dual-node probes (~35 min) without reset.
- **Multi-node airtime / BLE backpressure (BUILD 106–107):** LoRa→phone BLE notifies are skipped when the phone is not connected; signal-test probes are dropped if the BLE notify queue is nearly full. Probe TX uses **8 × 600 ms** slots (roster-aware when roster is enabled). Flood duplicate broadcast TX is suppressed under channel pressure **≥ 2**. Scheduler capacity **16 → 24**; ACK trackers **8 → 12**; pending-direct buffer **2 → 4**.
- **Field validation (BUILD 107):** dual-node rostered soak **~6 h** (~99.96% / 100% probe delivery); **2 h** roster simulation with **5** roster IDs and **2** live nodes (Sim-B) completed without lockup. Bench commands: `txprobe`, `stress on` / `stress off` (see [Serial Diagnostics](#serial-diagnostics-and-maintenance)).

**Multi-node capacity (planning guidance)**

- Firmware roster hard cap: **8** node IDs. For typical **GPS + chat** traffic, treat **≤ 5** rostered nodes as the practical production ceiling; **7–8** nodes are marginal for sustained airtime unless traffic is reduced. Roster must list **every** participating node ID on **every** node before relying on multi-node Signal Test or map sync.

**Battery display stability (from `factory_v0.0.3` / BUILD 87 onward)**

- **EMA** smoothing on cell millivolts to reduce ADC noise between intervals.
- **Single-step** percentage changes use a **millivolt hysteresis band** so small noise does not constantly tick the value.
- **Spike limiting:** raw sample vs previous EMA clamped by **`BATTERY_MAX_DELTA_MV_PER_SAMPLE`** (e.g. **±35 mV** per interval) to reject single bad ADC reads.
- **Warmup reads** before the first sample after link-up to reduce an optimistic first reading.
- **Displayed % does not increase** during a BLE session when only “recovery” from load sag occurs (no charge-detection pin in this design); the official app should use the **stabilized percentage** from the status packet.

**Wire protocol to the mobile app**

- `MESH_TYPE_STATUS`, local-node packet type **`0xFE`**, event **`0x43`** (`MESH_STATUS_EVENT_NODE_BATTERY`).
- **`detail`:** 0–100 (stabilized %).
- **`auxValue`:** cell voltage in **millivolts** (EMA; matches the same curve as `detail`).
- Related events on the same local status path include **`0x41`** (GPS), **`0x42`** (GPS interval), and **`0x44`** (firmware **BUILD** number - typically sent once per authenticated BLE session; **`detail`** `0`, **`auxValue`** = build).

The embedded firmware **BUILD** number appears in **startup serial logs** (e.g. `[MeshGrid] boot BUILD …`) and in the **`stats`** output line **`BUILD=`**; match this to the GitHub Release asset you flashed.

### Power and Signal Notes

- The firmware uses the E22 **AUX** pin as a module-ready / UART-idle signal before transmit
- AUX handling improves UART-side reliability, but it is **not** true RF carrier sensing and should not be treated as collision avoidance in the radio sense
- The current build does **not** drive E22 **M0/M1** configuration pins; if the module requires AT/configuration changes, that must be handled outside the normal runtime path
- GPS support is enabled in the current build, but the code can still report **no hardware** if a GPS receiver is not physically present or not producing valid NMEA data
- The radio module and ESP32 must share a stable power source and common ground; marginal power behavior will look like software instability even when the firmware is correct
- If a **battery** is used, verify divider wiring and **never** exceed the ESP32 ADC input limits on **GPIO34**; compare multimeter cell voltage to `auxValue` (mV) in the app if calibration seems off
- USB-only bench power without a cell may show **0%** or noisy ADC readings on **GPIO34** - that is expected if the sense node is floating or tied only to USB-derived rails

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

Official **binary** releases published in this repository are offered under **[CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/)** for **non-commercial** sharing of **unmodified** builds, with attribution. The full notice, including reserved rights of the copyright holder, is in **[`LICENSE`](LICENSE)**.

The copyright holder **retains all rights not granted** under that public license, including **commercial sale** of firmware or pre-flashed devices, **separate commercial or OEM agreements**, and **authorized distribution** channels.

### Third-party restrictions (unless separately licensed in writing)

- **Commercial** redistribution or resale of firmware binaries, or of hardware pre-loaded with them, **without authorization**.
- **Modified** firmware builds: creation or distribution of derivative works is not permitted under CC **NoDerivatives** for licensees.
- **Reverse engineering** or attempted extraction of **source code** where prohibited by applicable law or by contract.

**Client software:** Use with the **official MeshGrid mobile application** and supported ecosystem only, as described in this document. Third-party or unofficial clients are not supported.

By downloading, flashing, configuring, or using this firmware, you agree to comply with **[`LICENSE`](LICENSE)**, any **purchase or enterprise terms** that apply to how you obtained the software, and all applicable RF laws and regulations.

**CC BY-NC-ND and redistribution:** Non-commercial recipients may still **share unmodified copies** of published binaries (with attribution). If your product requires **no third-party redistribution** at all, you will typically need a **proprietary license or EULA** in addition to or instead of the public CC terms - contact the copyright holder for commercial licensing.

---
