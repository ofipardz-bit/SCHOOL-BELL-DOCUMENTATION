# 📖 User Manual — School Bell System

> Step-by-step guide for setting up and operating the Standalone Automated School Bell System.

---

## Table of Contents

1. [What You Need](#what-you-need)
2. [First-Time Setup](#first-time-setup)
3. [Connecting to the Web Dashboard](#connecting-to-the-web-dashboard)
4. [Managing the Schedule (Dashboard)](#managing-the-schedule-dashboard)
5. [Using the Physical Buttons](#using-the-physical-buttons)
6. [Setting the Time](#setting-the-time)
7. [Adding a Schedule Event (On-Device)](#adding-a-schedule-event-on-device)
8. [Adjusting Volume](#adjusting-volume)
9. [Testing the Bell](#testing-the-bell)
10. [Daily Operation](#daily-operation)
11. [After a Power Loss](#after-a-power-loss)
12. [Troubleshooting](#troubleshooting)

---

## What You Need

Before starting, make sure you have:

- ✅ The assembled School Bell device (powered on)
- ✅ A phone, tablet, or laptop with Wi-Fi
- ✅ A microSD card inserted into the DFPlayer Mini with MP3 files loaded
- ✅ A 3.5mm AUX cable connected to a PA system or amplifier

---

## First-Time Setup

### Step 1 — Prepare the SD Card

1. Format the microSD card as **FAT32**
2. Create a folder named exactly **`01`** (no slash, just the number)
3. Copy your MP3 bell audio files into it, named:
   - `001.mp3` — Track 1
   - `002.mp3` — Track 2
   - `003.mp3` — Track 3
   - and so on up to `255.mp3`
4. Insert the microSD card into the DFPlayer Mini module

> ⚠️ The folder **must** be named `01` and files **must** use 3-digit numbers. Any other naming will not play.

---

### Step 2 — Connect the AUX Cable

1. Plug a 3.5mm AUX cable into the **AUX output jack** on the device
2. Connect the other end to your **PA system or amplifier input**
3. Turn on the PA system and set it to the correct AUX input

---

### Step 3 — Power On the Device

1. Connect the 5V power adapter to the device
2. The OLED will show the boot sequence:

```
School Bell v1.0
Initializing...
```

Then each component status appears:

```
RTC:      OK
SPIFFS:   OK
DFPlayer: OK
Events:   0
```

3. After boot, the Wi-Fi info screen shows:

```
WiFi AP Ready
SSID:BellDashboard
IP: 192.168.4.1
PW: 12345678
```

4. The device then goes to the **main screen** showing current time and next bell

> If any component shows **FAIL**, see the [Troubleshooting](#troubleshooting) section.

---

## Connecting to the Web Dashboard

1. On your phone or laptop, open **Wi-Fi settings**
2. Connect to the network: **`BellDashboard`**
3. Enter the password: **`12345678`**
4. Open a web browser and go to: **`http://192.168.4.1`**

The dashboard will load and show the current schedule, clock, and controls.

> Works on any device — phone, tablet, or laptop. No internet needed.

---

## Managing the Schedule (Dashboard)

### Adding a Bell Event

1. Click **+ Add Event**
2. Select the **Day** from the dropdown (Mon–Sun)
3. Enter the **Hour** (0–23, 24-hour format)
4. Enter the **Minute** (0–59)
5. Enter the **Track number** (1–255, matches your MP3 file)
6. The file path preview (e.g. `/01/001.mp3`) updates automatically
7. Click **Save Event**

The event is saved immediately — no restart needed.

### Editing an Event

1. Find the event in the schedule table
2. Click the **pencil icon** on that row
3. Adjust the values
4. Click **Save Event**

### Deleting an Event

1. Find the event in the schedule table
2. Click the **trash icon** on that row
3. The event is removed immediately

### Filtering by Day

Click any day tab (**Mon, Tue, Wed**, etc.) at the top of the schedule table to filter events by that day. Click **All** to see the full schedule.

### Setting the RTC Time from Dashboard

1. Click **Set RTC Time**
2. Enter the correct Year, Month, Day, Hour, and Minute
3. Click **Apply Time**

The DS3231 RTC is updated immediately and will keep the time even after power loss.

---

## Using the Physical Buttons

| Button | What it does |
|--------|-------------|
| **OK** | Open menu / confirm selection |
| **UP** | Move up in menu / increase value |
| **DOWN** | Move down in menu / decrease value |
| **BACK** | Go back to the previous screen |
| **TEST** | Ring the bell immediately (works from any screen) |

---

## Setting the Time

### Via Physical Buttons

1. From the main screen, press **OK** to open the menu
2. Press **UP/DOWN** to highlight **Set Time**, press **OK**
3. The cursor starts on **Hr** (hour):
   - Press **UP** to increase, **DOWN** to decrease
   - Press **OK** to move to **Min** (minute)
4. On **Min**:
   - Press **UP/DOWN** to adjust
   - Press **OK** to confirm and save to DS3231
5. Press **BACK** at any point to cancel

### Via Web Dashboard

1. Open `http://192.168.4.1`
2. Click **Set RTC Time**
3. Fill in all date and time fields
4. Click **Apply Time**

---

## Adding a Schedule Event (On-Device)

1. Press **OK** → navigate to **Event** → press **OK**
2. Select the **day** using UP/DOWN, press **OK**
3. If there are existing events listed, press **OK** to open **Add Event**
   - (If the list is empty, Add Event opens automatically)
4. Adjust **Hr** with UP/DOWN, press **OK** to advance
5. Adjust **Min** with UP/DOWN, press **OK** to advance
6. Adjust **Trk** (track number) with UP/DOWN, press **OK** to save
7. The event is saved and you return to the event list

> To **delete** an event on-device: navigate to the event list for that day, highlight the event with UP/DOWN, and press **OK**.

---

## Adjusting Volume

### Via Physical Buttons

1. Press **OK** → navigate to **Volume** → press **OK**
2. Press **UP** to increase, **DOWN** to decrease
3. The bar graph updates in real time
4. Press **BACK** to return to the menu

### Via Web Dashboard

1. Open `http://192.168.4.1`
2. Drag the **Volume slider** in the left panel
3. The volume updates on the device within 1 second

Volume range is **0–30** (DFPlayer native scale), shown as a percentage on the main screen.

---

## Testing the Bell

### Via Physical Button

Press the **TEST** button from any screen at any time. The OLED will show:

```
TEST BELL
DFPlayer: OK
Playing track 1...
```

Track 1 (`/01/001.mp3`) will play through the AUX output.

### Via Web Dashboard

Click the **▶ Test Bell** button at the top of the schedule table. A toast notification confirms the bell was triggered.

---

## Daily Operation

Once set up, the device runs **fully automatically**:

- The bell rings at each scheduled time with no user action needed
- The OLED main screen always shows the current time and the next upcoming bell
- The Wi-Fi dashboard stays active — you can adjust the schedule at any time
- The watchdog timer automatically recovers the device if it ever freezes

**You only need to interact with the device to:**
- Change the schedule
- Adjust the time
- Adjust the volume
- Trigger a manual test bell

---

## After a Power Loss

The device is designed to recover automatically:

| What happens | How it's handled |
|---|---|
| Time is lost | DS3231 RTC has a CR2032 battery — time is kept |
| Schedule is lost | Schedule is saved in SPIFFS (internal flash) — survives power loss |
| A backup schedule exists | `/backup.csv` is loaded automatically if `/active.csv` is missing |
| Device just restarts | Boot sequence runs, schedule loads, normal operation resumes |

No user action is needed after a power cycle.

---

## Troubleshooting

| Symptom | Likely Cause | Fix |
|---------|-------------|-----|
| OLED shows `RTC: FAIL` | Wiring issue or no battery | Check SDA/SCL wiring; install CR2032 |
| OLED shows `DFPlayer: FAIL` | Wiring or missing resistor | Check GPIO16/17; verify 1kΩ on RX; reseat SD card |
| OLED shows `Events: 0` | No schedule loaded | Add events via web dashboard |
| No sound from AUX | Wrong output pins | Ensure DAC_L/DAC_R used — NOT SPK1/SPK2 |
| Dashboard not loading | Not connected to BellDashboard Wi-Fi | Reconnect to `BellDashboard` / `12345678` |
| Bell rings twice | (should not happen) | `lastFired` lock prevents this — reboot if it occurs |
| OLED is blank | `setTextColor` not set | Already fixed in firmware — re-flash if needed |
| Volume has no effect | DFPlayer not initialized | Check DFPlayer wiring and SD card |
| Time is wrong after power loss | CR2032 battery missing or dead | Install or replace the battery on the DS3231 module |
| Device randomly resets | Insufficient power | Use a 5V 2A adapter; check USB cable quality |

---

*For wiring details and pin maps, refer to `README.md` and `WIRING.md`.*
