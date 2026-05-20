# 🔔 Standalone Automated School Bell System
### ESP32-S3 · DS3231 RTC · DFPlayer Mini · SSD1306 OLED · Web Dashboard
Group Name: SOFIA LORIN PARDILLO AND ANGELA LLANTO

> A self-running, schedule-driven school bell system with a built-in web dashboard, OLED UI, and physical button controls.

---

## 📋 Table of Contents

1. [Project Overview](#project-overview)
2. [Hardware Requirements](#hardware-requirements)
3. [Pin Mapping](#pin-mapping)
4. [Wiring Diagram](#wiring-diagram)
5. [SD Card File Structure](#sd-card-file-structure)
6. [Schedule File Format](#schedule-file-format)
7. [Firmware Setup](#firmware-setup)
8. [Web Dashboard](#web-dashboard)
9. [OLED Menu Guide](#oled-menu-guide)
10. [Boot Sequence](#boot-sequence)
11. [Known Issues & Notes](#known-issues--notes)
12. [Grading Checklist](#grading-checklist)

---

## Project Overview

This system automatically rings a school bell at scheduled times using a CSV schedule stored in SPIFFS (ESP32 internal flash). It reads the current time from a battery-backed DS3231 RTC, plays MP3 audio through a DFPlayer Mini (output via **3.5mm AUX cable** to PA system), and provides a 5-button OLED interface for on-device configuration.

A built-in **Wi-Fi web dashboard** (Access Point mode) allows full schedule management from any phone or laptop — no internet required.

### Key Features

- ✅ Auto bell triggering from CSV schedule (day + time)
- ✅ No double-trigger within the same minute (`lastFired` key lock)
- ✅ DS3231 RTC with battery backup — survives power loss
- ✅ DFPlayer Mini audio → **AUX out (3.5mm jack)** → PA system
- ✅ SSD1306 OLED: current time + next bell event
- ✅ 5-button physical menu (UP / DOWN / OK / BACK / TEST)
- ✅ Wi-Fi AP web dashboard (`BellDashboard` / `12345678`)
- ✅ SPIFFS schedule storage at `/active.csv` + `/backup.csv`
- ✅ Boot status display: RTC / SPIFFS / DFPlayer individually
- ✅ Watchdog timer (10s timeout) for crash recovery

---

## Hardware Requirements

| # | Component | Notes |
|---|-----------|-------|
| 1 | ESP32-S3 N16R8 DevKit | Main microcontroller |
| 2 | DS3231 RTC Module | With CR2032 battery installed |
| 3 | DFPlayer Mini MP3 Module | Audio output via AUX (not speaker terminals) |
| 4 | SSD1306 0.96" OLED (I2C) | 128×64 pixels |
| 5 | microSD Card (8–32 GB) | FAT32 formatted, for DFPlayer audio only |
| 6 | 5× Momentary Push Buttons | UP, DOWN, OK, BACK, TEST |
| 7 | 3.5mm AUX Jack (female) | Connected to DAC_L / DAC_R / GND |
| 8 | 1 kΩ Resistor | DFPlayer RX line protection |
| 9 | 5V 2A Power Adapter | ESP32 + DFPlayer supply |
| 10 | 10 µF Electrolytic Capacitors (×2) | Optional: AUX DC offset filtering |

---

## Pin Mapping

### I²C Bus — OLED + RTC

| Signal | ESP32-S3 GPIO |
|--------|--------------|
| SDA    | GPIO **8**   |
| SCL    | GPIO **9**   |

> Both SSD1306 OLED and DS3231 RTC share the same I²C bus (`0x3C` and `0x68` respectively).

### DFPlayer Mini

| DFPlayer Pin | Connection |
|---|---|
| VCC | 5V |
| GND | GND |
| RX | GPIO **17** via 1 kΩ resistor |
| TX | GPIO **16** |
| DAC_L | AUX Jack Left (+ optional 10 µF cap) |
| DAC_R | AUX Jack Right (+ optional 10 µF cap) |
| GND (audio) | AUX Jack GND |

> ⚠️ **SPK1 / SPK2 terminals are NOT used.** Audio routes through DAC_L/DAC_R → 3.5mm AUX jack → PA system.
> Uses `HardwareSerial(1)` on the ESP32-S3.

### Buttons

| Button | GPIO | Function |
|--------|------|----------|
| UP     | GPIO **15** | Scroll up / increment |
| DOWN   | GPIO **7**  | Scroll down / decrement |
| OK     | GPIO **5**  | Confirm / select |
| BACK   | GPIO **4**  | Go back / cancel |
| TEST   | GPIO **2**  | Manually trigger bell now |
| RESET  | EN → GND    | Hardware reset |

> All buttons use `INPUT_PULLUP` — one side to GPIO, other side to GND.

### Power Rails

| Component | Voltage |
|-----------|---------|
| ESP32-S3 | 5V (USB or adapter) |
| DFPlayer Mini | 5V |
| SSD1306 OLED | 3.3V |
| DS3231 RTC | 3.3V |

---

## Wiring Diagram

```
                         ESP32-S3 N16R8
                    ┌─────────────────────┐
         3.3V ──────┤ 3V3            GPIO2 ├──── [TEST Button] ── GND
          GND ──────┤ GND            GPIO4 ├──── [BACK Button] ── GND
           5V ──────┤ 5V             GPIO5 ├──── [OK Button]   ── GND
                    │                GPIO7 ├──── [DOWN Button] ── GND
  SDA (OLED+RTC) ──┤ GPIO8         GPIO15 ├──── [UP Button]   ── GND
  SCL (OLED+RTC) ──┤ GPIO9         GPIO16 ├──── DFPlayer TX
                    │               GPIO17 ├──[1kΩ]── DFPlayer RX
                    └─────────────────────┘

  DS3231 RTC                SSD1306 OLED
  ┌──────────┐              ┌──────────┐
  │ VCC─3.3V │              │ VCC─3.3V │
  │ GND─GND  │              │ GND─GND  │
  │ SDA─GPIO8│              │ SDA─GPIO8│
  │ SCL─GPIO9│              │ SCL─GPIO9│
  └──────────┘              └──────────┘

  DFPlayer Mini             3.5mm AUX Jack
  ┌──────────┐              ┌─────────────────────────────┐
  │ VCC─ 5V  │              │ TIP  (L) ── DAC_L  [+10µF optional]
  │ GND─GND  │              │ RING (R) ── DAC_R  [+10µF optional]
  │ RX──[1kΩ]───GPIO17      │ GND      ── GND
  │ TX──────────GPIO16      └─────────────────────────────┘
  │ DAC_L───────────────────── AUX Left
  │ DAC_R───────────────────── AUX Right
  └──────────┘
```

---

## SD Card File Structure

The microSD card is used **only for DFPlayer audio files**. The schedule is stored separately in ESP32 SPIFFS (internal flash).

Format the microSD card as **FAT32**, then create this structure:

```
microSD Card (FAT32)
└── 01/
    ├── 001.mp3        ← Track 1
    ├── 002.mp3        ← Track 2
    └── 003.mp3        ← Track 3
```

> ⚠️ **DFPlayer Hardware Limitation:** Folder names must be numeric (`/01/`) and filenames must be numeric (`001.mp3`). No other naming works.

### Track Naming Reference

| Track | Filename | Suggested Use |
|-------|----------|---------------|
| 1 | `001.mp3` | Class Start / Morning Bell |
| 2 | `002.mp3` | Break / Recess Bell |
| 3 | `003.mp3` | Dismissal Bell |
| 4+ | `00N.mp3` | Custom (up to 255) |

---

## Schedule File Format

The schedule lives in **SPIFFS** at `/active.csv`. A backup is automatically saved to `/backup.csv` on every change. Both files are managed entirely through the web dashboard.

### CSV Format

```csv
DAY,TIME,TRACK
MON,07:30,1
MON,08:45,2
MON,10:00,3
TUE,07:30,1
WED,07:30,1
THU,07:30,1
FRI,07:30,1
FRI,15:30,3
```

### Field Reference

| Field | Values | Description |
|-------|--------|-------------|
| `DAY` | MON / TUE / WED / THU / FRI / SAT / SUN | Day of the week |
| `TIME` | HH:MM (24-hour) | Scheduled ring time |
| `TRACK` | 1 – 255 | MP3 track number in `/01/` folder |

> **Max events:** 175 entries (`#define MAX_EVENTS 175`)

---

## Firmware Setup

### Required Libraries

Install via Arduino IDE Library Manager:

| Library | Install Name |
|---------|-------------|
| Adafruit SSD1306 | `Adafruit SSD1306` (auto-installs Adafruit GFX) |
| Adafruit GFX | Installed automatically with SSD1306 |
| RTClib | `RTClib` by Adafruit |
| DFRobotDFPlayerMini | `DFRobotDFPlayerMini` |

### Built-in ESP32 Headers (no install needed)

```cpp
#include <Wire.h>
#include <SPI.h>
#include "SPIFFS.h"
#include <WiFi.h>
#include <WebServer.h>
#include "esp_task_wdt.h"
```

### Board Setup (Arduino IDE)

1. Add ESP32 board URL:
   `https://raw.githubusercontent.com/espressif/arduino-esp32/gh-pages/package_esp32_index.json`
2. **Tools → Board → ESP32 Arduino → ESP32S3 Dev Module**
3. **Upload Speed:** `921600`
4. **Partition Scheme:** `Default 4MB with spiffs`
5. **PSRAM:** `OPI PSRAM` (N16R8 variant)

### Flashing

1. Open `SchoolBell_ESP321.ino` in Arduino IDE
2. Ensure `dashboard_html.h` is in the **same folder**
3. Select the correct COM port
4. Click **Upload**

---

## Web Dashboard

| Setting | Value |
|---------|-------|
| SSID | `BellDashboard` |
| Password | `12345678` |
| URL | `http://192.168.4.1` |

### Features

- 📅 View schedule — filterable by day
- ➕ Add / ✏️ Edit / 🗑️ Delete bell events
- 🔔 Next Bell widget with live countdown
- 🕐 Set RTC date and time from browser
- 🔊 Volume slider (DFPlayer level 0–30)
- 🧪 Test Bell — triggers Track 1 instantly
- 📊 Stats panel — total events, active days, unique tracks

---

## OLED Menu Guide

### Main Screen

```
WED 2025-01-14
  12:34:05
NEXT: 13:00 WED
VOL:67%    [TEST]
```

### Button Reference

| Button | Action |
|--------|--------|
| OK | Open menu |
| UP / DOWN | Navigate / adjust value |
| OK | Select / confirm field |
| BACK | Go back one level |
| TEST | Ring bell immediately (works on any screen) |

### Menu Tree

```
Main Menu
├── 1. Set Time
│   ├── Hr  → UP/DOWN to adjust, OK to advance
│   ├── Min → UP/DOWN to adjust, OK to confirm
│   └── Writes to DS3231
│
├── 2. Set Sched
│   ├── Day (SUN–SAT)
│   ├── Hr  (0–23)
│   ├── Min (0–59)
│   ├── Trk (1–255)
│   └── Saves event to SPIFFS
│
├── 3. Event
│   ├── Day Select
│   ├── Event List for selected day
│   │   ├── OK on event → Delete it
│   │   └── OK when list empty → Add Event
│   └── Add Event (Hr / Min / Trk)
│
└── 4. Volume
    └── UP/DOWN adjusts level (0–30), shown as bar
```

---

## Boot Sequence

On every power-up, OLED shows each component one by one:

```
School Bell v1.0        School Bell v1.0
Initializing...    →    RTC:      OK
                        SPIFFS:   OK
                        DFPlayer: OK
                        Events:   10
```

Then transitions to Wi-Fi info screen:

```
WiFi AP Ready
SSID:BellDashboard
IP: 192.168.4.1
PW: 12345678
```

### Failure States

| Message | Meaning | Fix |
|---------|---------|-----|
| `RTC: FAIL` | DS3231 not detected | Check I2C wiring, CR2032 battery |
| `SPIFFS: FAIL` | Internal flash error | Re-flash firmware |
| `DFPlayer: FAIL` | DFPlayer not responding | Check wiring, 1kΩ resistor, SD card |
| `Events: 0` | No schedule loaded | Add events via web dashboard |

> Even if DFPlayer fails at boot, the device still runs. TEST button and schedule check will work once DFPlayer is detected.

---

## Known Issues & Notes

| Issue | Status | Notes |
|-------|--------|-------|
| DFPlayer requires `/01/` numeric folder | By design | Hardware limitation of DFPlayer Mini |
| `display.printf()` unreliable on SSD1306 | Fixed | Uses `print()`/`println()` with `setCursor()` |
| `display.setTextColor(WHITE)` must be in setup | Fixed | Blank display if omitted |
| Zero-padding time fields | Fixed | Uses `printTwoDigit()` helper |
| DFPlayer volume range | Fixed | Mapped into 0–30 via `map()` |
| Double-trigger prevention | Fixed | `lastFired` string key per day-hour-minute |
| Duplicate event prevention | Fixed | Checked on both physical buttons and web add |
| AUX DC pop on audio start | Optional | Add 10 µF caps on DAC_L / DAC_R lines |

---

## Grading Checklist

### Functionality (40%)
- [x] Auto schedule ring works from CSV
- [x] TEST ring works via button and dashboard
- [x] Menu works — Set Time, Set Sched, Event, Volume
- [x] No double-trigger within same minute
- [x] Wi-Fi dashboard for full schedule management

### Reliability (30%)
- [x] Survives power cycle (DS3231 battery backup)
- [x] Watchdog timer prevents lockup
- [x] Schedule backed up to SPIFFS on every change
- [x] Clear OLED error messages on component failure

### Workmanship (20%)
- [x] Clean wiring, no loose connections
- [x] AUX output via DAC (not raw speaker terminals)
- [x] Proper enclosure with accessible ports

### Documentation (10%)
- [x] Pin mapping table
- [x] Wiring diagram
- [x] SD card format explanation
- [x] User manual — see `MANUAL.md`

---

## Project Info

| Field | Value |
|-------|-------|
| Course | Electronics / Embedded Systems |
| Group | Group 2 — Pardillo / Llanto / Muyco |
| Microcontroller | ESP32-S3 N16R8 |
| IDE | Arduino IDE |
| Firmware Files | `SchoolBell_ESP321.ino`, `dashboard_html.h` |

---

*Built with ❤️ using ESP32-S3, DS3231, DFPlayer Mini, and SSD1306 OLED.*
