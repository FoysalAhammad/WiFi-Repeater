<p align="center">
  <img src="assets/banner.svg" alt="WiFi Repeater banner" width="100%">
</p>

<div align="center">

# 🔁 WiFi Repeater

**NAT range extender · softAP + STA · web & serial config**

![platform](https://img.shields.io/badge/platform-ESP32-1e88e5?style=for-the-badge)
![mode](https://img.shields.io/badge/mode-NAT_Repeater-00e5a0?style=for-the-badge)
![license](https://img.shields.io/badge/license-Proprietary-6f42c1?style=for-the-badge)
![storage](https://img.shields.io/badge/storage-NVS-6f42c1?style=for-the-badge)
![status](https://img.shields.io/badge/status-stable-3fb950?style=for-the-badge)

![divider](assets/divider.svg)

**Pre-built firmware · no source code required to use it**

</div>

> A proper **NAT-based WiFi range extender**. The ESP32 holds a **STA** link to your router and simultaneously runs a **softAP** for clients; lwIP's `CONFIG_LWIP_IPV4_NAPT` (compiled into the ESP32 core) forwards traffic between them. Configure it from the web page or the serial console — settings live in **Preferences (NVS)** and survive power cycles.

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

Single radio ⇒ both links share one channel, so expect roughly **half** of the router's throughput — that is physics, not a bug.

## ✨ Features

| ✨ Feature | 📝 Description |
|:--|:--|
| 🔁 **True NAT** | lwIP NAPT bridges softAP clients to the STA uplink |
| 🌐 **Web config** | Set router SSID/pass + AP name/pass from the browser |
| ⌨️ **Serial console** | 9 commands — `ssid pass apssid appass show save reset reboot` |
| 💾 **NVS storage** | `Preferences` keeps config across reboots |
| 🏭 **Factory reset** | `reset` wipes credentials instantly |
| 📡 **Same-channel** | STA + AP share the single 2.4 GHz radio → no radio conflict |

## 📦 Firmware files

> ⏳ **The `.bin` files are attached to each release** — the table below lists exactly what ships (built, size-checked and SHA-256 verified).

Flash the file that matches your workflow:

| File | Flash offset | Size | SHA-256 (first 16) |
|:--|:--|:--|:--|
| `WiFiRepeater-full.bin` | `0x0` | 4.0 MB | `0e3d4da38baa08b2…` |
| `WiFiRepeater-app.bin` | `0x10000` | 914.6 KB | `932bf404afed1e62…` |
| `WiFiRepeater-bootloader.bin` | `0x1000` | 24.4 KB | `47bbbfca119fe871…` |
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

1. Flash, power on, then join the open **`ESP_Repeater`** network.
2. Open **`http://192.168.4.1`** and fill in **your router's SSID + password**.
3. Optionally rename the repeater's own network and give it a password (leave `none` for open).
4. Press **SAVE** — the board stores the config in flash and reboots.
5. Join the **new repeater network** — internet now works through it.
6. Done? Everything survives power cycles. Use `reset` on the serial console for a factory reset.

## 🔑 Login / Access

| Item | Value |
|:--|:--|
| 📶 **WiFi network (AP)** | `ESP_Repeater` |
| 🔑 **AP password** | `(open — no password)` |
| 🌍 **Panel URL** | **`http://192.168.4.1`** |
| 🖥️ **Control IP** | `192.168.4.1` |
| ⌨️ **Serial baud rate** | `115200` |


## 🌐 Web panel & API

Everything on the panel is a plain GET request, so you can also script it with `curl`:

| Method | Endpoint | Description |
|:--|:--|:--|
| `GET` | `/` | Configuration form (uplink + AP credentials) |
| `GET` | `/save` | Persist settings to NVS and reboot |

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
