<p align="center">
  <img src="assets/banner.svg" alt="WiFi Repeater banner" width="100%">
</p>

<div align="center">

# 🔁 WiFi Repeater

**NAT range extender · hardened web config · JSON status · mDNS**

![platform](https://img.shields.io/badge/platform-ESP32-1e88e5?style=for-the-badge)
![mode](https://img.shields.io/badge/mode-NAT_Repeater-00e5a0?style=for-the-badge)
![license](https://img.shields.io/badge/license-Proprietary-6f42c1?style=for-the-badge)
![storage](https://img.shields.io/badge/storage-NVS-6f42c1?style=for-the-badge)
![status](https://img.shields.io/badge/status-stable-3fb950?style=for-the-badge)

![divider](assets/divider.svg)

**Pre-built firmware · no source code required to use it**

</div>

> A production-grade **NAT-based WiFi range extender**. The ESP32 holds a **STA** link to your router and simultaneously runs a **softAP** for clients; lwIP's `CONFIG_LWIP_IPV4_NAPT` forwards traffic between them. The configuration panel is behind **HTTP Basic Auth**, never echoes stored passwords back to the browser, HTML-escapes every field and validates input lengths. Uplink reconnects use **exponential back-off**, NAPT re-arms itself after every reconnect, and all settings live in **Preferences (NVS)**.

<details>
<summary>📑 <b>Table of Contents</b></summary>

- [🎯 What it is for](#-what-it-is-for)
- [🧰 Supported hardware](#-supported-hardware)
- [🧠 How it works](#-how-it-works)
- [✨ Features](#-features)
- [📦 Firmware files](#-firmware-files)
- [🔥 How to Flash](#-how-to-flash)
- [▶️ How to Use](#️-how-to-use)
- [🔑 Login / Access](#-login--access)
- [🌐 Web panel & API](#-web-panel--api)
- [📚 Wiki](#-wiki)
- [⚖️ Legal](#️-legal)

</details>

---

## 🎯 What it is for

- 📶 **Dead-zone killer** — bring WiFi to a balcony, garage or workshop
- 🏠 **Guest network** bridged to your main router
- 🔌 **IoT uplink** for printers / cameras that live out of range
- 🎓 **Networking lab** — watch IPv4 NAT (NAPT) working in lwIP

## 🧰 Supported hardware

<p align="center">
  <img src="assets/board.svg" alt="ESP32 development board" width="780">
</p>

| Board | Chip | Flash | USB |
|:--|:--|:--|:--|
| **ESP32 Dev Module (esp32dev)** | ESP32 (Xtensa dual-core 240 MHz) | 4 MB | Micro-USB (CP2102/CH340) |

> ✅ Works out of the box with the exact board in the picture — no wiring, no soldering, no extra parts. Just a USB cable and 5 V.

## 🧠 How it works

![flow](assets/flow.svg)

Single radio ⇒ both links share one channel, so expect roughly **half** of the router's throughput — that is physics, not a bug. The panel is protected by HTTP Basic Auth and stored passwords are never echoed back to the browser.

## ✨ Features

| ✨ Feature | 📝 Description |
|:--|:--|
| 🔁 **True NAT** | lwIP NAPT bridges softAP clients to the STA uplink |
| 🔐 **HTTP Basic Auth** | Panel + `/save` need login; defaults `admin` / `admin` (change it) |
| 🙈 **No password echo** | Stored passwords are never sent back to the browser |
| 📊 **JSON status** | Open `GET /api/status` for uptime, RSSI, clients, NAPT, heap |
| 📶 **Self-healing uplink** | Exponential back-off (5→10→20→30 s) + `setAutoReconnect` |
| 🎚️ **NAPT re-arm** | NAT is re-enabled automatically after every STA reconnect |
| 🏷️ **mDNS** | Reach the panel at `http://repeater.local` |
| 💡 **Status LED** | Solid = uplink OK · slow blink = reconnecting · fast blink = needs config |
| 🏭 **Factory reset** | Serial `reset` **or hold BOOT 5 s** → wipes NVS and reverts defaults |
| ⌨️ **Serial console** | 13 commands incl. `user` / `hpass` / `status` / `version` |

## 📦 Firmware files

> ⏳ **The `.bin` files are attached to each release** — the table below lists exactly what ships (built, size-checked and SHA-256 verified).

Flash the file that matches your workflow:

| File | Flash offset | Size | SHA-256 (first 16) |
|:--|:--|:--|:--|
| `WiFiRepeater-full.bin` | `0x0` | 4.0 MB | `b7ba0493eba96844…` |
| `WiFiRepeater-app.bin` | `0x10000` | 967.7 KB | `5563bff34c890211…` |
| `WiFiRepeater-bootloader.bin` | `0x1000` | 22.9 KB | `7c5e6c42dcd3b658…` |
| `WiFiRepeater-partitions.bin` | `0x8000` | 3.0 KB | `aaae2888c5a6a348…` |
| `WiFiRepeater-ota.bin` | `0xE000` | 8.0 KB | `f94c5d786a7a8fab…` |

## 🔥 How to Flash

### ⚡ Method 1 — ESP Web Flasher (easiest, nothing to install)

1. Use **Chrome** or **Edge** and open **[espressif.github.io/esptool-js](https://espressif.github.io/esptool-js/)**.
2. Connect the board with a USB cable, then click **CONNECT** and pick the serial port.
3. Click **+ ADD FILE** and add the firmware:

**Option A — one file (recommended):**

| File | Offset |
|:--|:--|
| `WiFiRepeater-full.bin` | `0x0` |

**Option B — individual parts:**

| File | Offset |
|:--|:--|
| `WiFiRepeater-app.bin` | `0x10000` |
| `WiFiRepeater-bootloader.bin` | `0x1000` |
| `WiFiRepeater-partitions.bin` | `0x8000` |
| `WiFiRepeater-ota.bin` | `0xE000` |


4. Set **BAUD** to `921600`, press **ERASE FLASH**, then **START**.
5. Wait for `Hard resetting` — the firmware is on the board.

### 🖥️ Method 2 — `esptool` from a terminal

```bash
esptool.py --chip esp32 --port /dev/ttyUSB0 --before default_reset --after hard_reset \
  write_flash 0x0 firmware/WiFiRepeater-full.bin
```

### 🪟 Method 3 — Windows users

Use the official **[Espressif Flash Download Tool](https://www.espressif.com/en/support/download/other-tools)** — set the crystal `26 MHz`, add the same files/offsets as in Method 1, and press **START**.

> ⚠️ **Wrong board detected?** Hold the **BOOT** button while flashing, release it after the download starts.

## ▶️ How to Use

1. Flash, power on, then join **`ESP_Repeater`** — password **`repeater123`**.
2. Open **`http://192.168.4.1`** (or **`http://repeater.local`**) and log in — default **`admin` / `admin`**.
3. Fill in **your router's SSID + password**; optionally rename the repeater AP (type `none` for open).
4. Press **Save & Reboot** — input is validated, stored in NVS and the board restarts.
5. Join the **new repeater network** — internet now works through it (NAPT).
6. Monitor it with `GET /api/status` (JSON) or the LED (solid = link up).
7. Clean slate? Serial `reset`, or **hold BOOT 5 seconds** for a factory reset.

## 🔑 Login / Access

| Item | Value |
|:--|:--|
| 📶 **WiFi network (AP)** | `ESP_Repeater` |
| 🔑 **AP password** | `repeater123` |
| 🌍 **Panel URL** | **`http://192.168.4.1 (or http://repeater.local)`** |
| 🖥️ **Control IP** | `192.168.4.1` |
| ⌨️ **Serial baud rate** | `115200` |
| 🔐 Panel login | `admin / admin — change it on the panel` |
| 🔑 Factory reset | `hold BOOT 5 s — or serial reset` |

## 🌐 Web panel & API

Endpoints are plain HTTP — script them with `curl`:

| Method | Endpoint | Description |
|:--|:--|:--|
| `GET` | `/` | Configuration form — **HTTP Basic Auth required** |
| `POST` | `/save` | Validate + persist to NVS, then reboot (password never in the URL) |
| `GET` | `/api/status` | JSON: uptime, STA/RSSI, clients, NAPT, heap, version (open) |

```bash
# example
curl "http://192.168.4.1/api/status"
```

## 📚 Wiki

Full reference, every endpoint, serial command, defaults, troubleshooting and FAQ:

**📖 [`WIKI.md`](WIKI.md)**

## ⚠️ Legal

⚠️ **Educational / authorized testing only.** Run this firmware **only** on hardware and networks **you own or have explicit written permission to test**. Unauthorized jamming, deauthentication, evil-twin phishing or traffic interception is **illegal** in most jurisdictions. You are solely responsible for how you use this code.

---

<div align="center">

![firmware](https://img.shields.io/badge/firmware-prebuilt-3fb950?style=for-the-badge)
![board](https://img.shields.io/badge/board-esp32-00979d?style=for-the-badge)
![docs](https://img.shields.io/badge/docs-wiki-0d6efd?style=for-the-badge)
![copyright](https://img.shields.io/badge/copyright-2026-6f42c1?style=for-the-badge)

**© 2026 — All rights reserved.** Firmware is sold as-is for **authorized testing on networks you own**. Redistribution of this repository's files is not permitted without written permission.

</div>
