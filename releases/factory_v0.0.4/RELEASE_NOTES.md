# MeshGrid-Node firmware v0.0.4 (BUILD 102)

**Release assets (this folder):**

| File | Use |
|------|-----|
| **`factory_v0.0.4.bin`** | **Recommended** — single merged image, flash at **`0x0000`** |
| `factory_v0.0.4.bin.sha256` | Checksum for the merged image |
| `bootloader.bin` | Multi-file flash @ `0x1000` |
| `partitions.bin` | Multi-file flash @ `0x8000` |
| `boot_app0.bin` | Multi-file flash @ `0xe000` |
| `firmware.bin` | Multi-file flash @ `0x10000` (app only; same BUILD as merged) |

**SHA256 (`factory_v0.0.4.bin`):** see `factory_v0.0.4.bin.sha256`

**Previous public release:** `factory_v0.0.3.bin` (BUILD 87) — GitHub asset was the merged image only; SHA256 was listed in the release description text.

**Verify after flash:** USB serial **115200** → `[MeshGrid] boot BUILD 102` or `stats` → `BUILD=102`.

---

## Summary (user-facing)

This release improves **long-running stability** when the official app runs **Signal Test** or heavy BLE + LoRa traffic, fixes **mesh line stroke width** relay for shapes, and adds **serial bench commands** for field validation. No provisioning or MeshLink wire format change is required if you already use v0.0.3 with the same mesh password.

---

## BUILD 102 — Signal-test / sustained probe stability (critical)

**Problem addressed:** After hundreds of Signal Test probes (~370+), nodes could appear **frozen** until manual reset.

**Root cause:** BLE notify queue saturation (status + RF telemetry + 50 ms delay per notify), hold-ring overflow, and LoRa scheduler slots stuck on repeated `DIRECT_TX` deferrals.

**Fixes:**

- Drop disposable **STATUS** / **RF_LINK** notifies when the BLE notify queue is under pressure (metric: `pressure_drop` in `stats`).
- Do not re-queue failed **STATUS** delivers (avoids BleTask spin).
- **Signal-test probes** (numeric mesh chat body + callsign prefix): skip `QUEUED` BLE status, skip post-RX E22 UART register reads for RF telemetry, reduce post-notify delay **50 ms → 2 ms**.
- Reclaim **stuck `SCHED_DIRECT_TX`** slots after **4.5 s**.
- BLE notify **hold ring 8 → 24** slots.

**Validation:** Dual-node LoRa probe stress — **420** probes per node (~35 min) — **no lockup**, no watchdog reset.

**New serial commands (115200 baud):**

- `txprobe CALLSIGN SEQ` — enqueue one LoRa signal-test-style probe (e.g. `txprobe ALPHA 42`).
- `stress on` / `stress on BRAVO1` / `stress off` — automatic probe every 5 s (bench use).

---

## BUILD 101 — Mesh line stroke width on peers

- Cache **shape stroke style** from LoRa `SHAPE_STYLE` / embedded BLE style.
- When relaying **LINE** geometry to iOS, append **4-byte stroke tail** on BLE notify when cache hit (fixes peers seeing default ~4 pt width).

---

## BUILD 100 — Embedded shape style from app

- iOS can send **stroke/style tail** after shape geometry on BLE.
- Firmware extracts and queues **derived SHAPE_STYLE** LoRa packet (retries under load).

---

## BUILD 97 — Firmware build telemetry

- Reports **BUILD** number to the app once per authenticated BLE session (`MESH_STATUS_EVENT_NODE_FIRMWARE_BUILD`).

---

## BUILD 93 — LoRa RF telemetry (optional E22 registers)

- RSSI/SNR sampling via E22 UART registers when enabled at boot.
- **RF_LINK** status events toward the phone (rate-limited).

---

## Compatibility

| Item | Notes |
|------|--------|
| **Mesh password** | Same as v0.0.3 — no re-provision required for upgrade |
| **NVS** | Build number change clears **pending TX replay** queue only (existing behavior) |
| **Official MeshGrid app** | Signal Test + map layers benefit from BUILD 102; flash nodes before relying on long Signal Test runs |
| **Flash (merged)** | `esptool.py --chip esp32 --port <PORT> --baud 460800 write_flash -z 0x0 factory_v0.0.4.bin` |
| **Flash (multi-file)** | `bootloader.bin` @ `0x1000`, `partitions.bin` @ `0x8000`, `boot_app0.bin` @ `0xe000`, `firmware.bin` @ `0x10000` (see README) |

---

## Flash size

- Merged image size: **~1.63 MB** (4 MB flash board, min_spiffs / 1.9 MB app partition).

---

## Internal limits (unchanged unless noted)

- ACK slots: 8 | Scheduler: 16 | Replay slots: 24 | BLE notify queue: 64 | **Hold ring: 24** (was 8)
