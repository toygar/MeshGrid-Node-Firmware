# MeshGrid ESP32 - factory_v0.0.5 (BUILD 107)

**Release asset:** `factory_v0.0.5.bin` — single merged image, flash at **`0x0000`**.

**Previous public release:** `factory_v0.0.4.bin` (BUILD 102).

**SHA256:** `16c23d2792cc1b6c7748049f4eb1385659570a54a52c16f95b0f33cc723cbcf1`

**Verify after flash:** USB serial **115200** → `[MeshGrid] boot BUILD 107` or `stats` → `BUILD=107`.

---

## Summary (user-facing)

This release improves **multi-node LoRa airtime sharing**, **BLE backpressure** under sustained Signal Test / probe load, and **scheduler capacity** for rostered deployments. No provisioning or MeshLink wire format change is required if you already use v0.0.4 with the same mesh password and roster IDs.

---

## BUILD 107 — Roster-aware probe slotting + queue headroom

**Problem addressed:** Under rostered multi-node stress, probe collisions and scheduler/ACK saturation could reduce delivery rate and stress BLE when a phone stays connected.

**Fixes:**

* **Roster-aware signal-test probe slots:** when roster is enabled, each node’s probe TX delay uses its index in the **sorted** roster ID list (not CSV order); without roster, callsign hash **`% 8`**.
* **ACK trackers:** **8 → 12**
* **Pending direct staging:** **2 → 4**

**Validation:**

* Dual-node rostered soak **~6 h** — **~99.96% / 100%** probe delivery, no lockup
* **Sim-B:** roster **5 IDs + 2 live nodes**, **2 h** — completed without lockup

---

## BUILD 106 — Probe airtime + BLE backpressure (Faz 1)

**Fixes:**

* Signal-test probe slotting: **2 slots @ 2500 ms** → **8 slots @ 600 ms**
* Skip **flood duplicate** broadcast TX when channel pressure **≥ 2**
* **Scheduler TX slots:** **16 → 24**
* Do not enqueue LoRa→BLE notify when phone **not connected**; drop signal-test probes when BLE notify queue is nearly full

**Validation:** 10 min dual-node stress — **0** `bleNotifyQueue full` (vs baseline BUILD 105 long soak)

---

## BUILD 105 — BLE connection telemetry + notify queuing

* Improved BLE disconnect reason reporting and connection-state handling
* Refined BLE notify queuing under load

---

## Compatibility

| Item | Notes |
| --- | --- |
| **Mesh password** | Same as v0.0.4 — no re-provision required for upgrade |
| **Roster** | Manual on every node; must include all participating node IDs for multi-node use |
| **NVS** | Build number change clears **pending TX replay** queue only (existing behavior) |
| **Official MeshGrid app** | Signal Test + map layers benefit from BUILD 106–107 |
| **Flash** | `esptool.py --chip esp32 --port <PORT> --baud 460800 write_flash -z 0x0 factory_v0.0.5.bin` |

---

## Flash size

* Merged image size: **~1.63 MB** (4 MB flash board, min_spiffs / 1.9 MB app partition)

---

## Internal limits (changed since BUILD 102)

| Limit | BUILD 102 | BUILD 107 |
| --- | --- | --- |
| ACK slots | 8 | **12** |
| Scheduler slots | 16 | **24** |
| Pending direct | 2 | **4** |
| BLE hold ring | 24 | 24 |
| Probe slots | — | **8 × 600 ms** (roster-aware) |

**Capacity planning:** firmware roster cap **8**; practical production ceiling for GPS+chat traffic **≤ 5** nodes; **7–8** marginal.

---

## Serial bench commands (115200 baud)

* `txprobe CALLSIGN SEQ` — e.g. `txprobe ALPHA 42`
* `stress on` / `stress on BRAVO1` / `stress off` — automatic probe every 5 s (bench use)
