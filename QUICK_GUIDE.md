# 📋 Quick Guide — School Bell System

---

## ⚡ Turning On the Device

1. Plug in the **5V power adapter**
2. Wait for the OLED to finish the boot screen
3. The main screen will appear showing the current time

```
WED 2026-05-20
  07:51:49
NEXT: --:--
VOL:66%   [TEST]
```

✅ Device is ready when you see the clock running.

---

## 🔘 What Each Button Does

```
  [BLUE]  [RED]  [WHITE]  [GREEN]  [YELLOW]
    UP     DOWN    OK       BACK     TEST
```

| Button | Color | What it does |
|--------|-------|-------------|
| UP | 🔵 Blue | Move up / increase number |
| DOWN | 🔴 Red | Move down / decrease number |
| OK | ⚪ White | Open menu / confirm |
| BACK | 🟢 Green | Go back / cancel |
| TEST | 🟡 Yellow | Ring the bell right now |

> Press **TEST** anytime to ring the bell immediately.

---

## 📅 Setting Up a Schedule

The easiest way is through the **web dashboard** on your phone.

### Step 1 — Connect to the device Wi-Fi
- Open Wi-Fi on your phone
- Connect to: **`BellDashboard`**
- Password: **`12345678`**

### Step 2 — Open the dashboard
- Open your browser (Chrome, Safari, etc.)
- Go to: **`http://192.168.4.1`**

### Step 3 — Add a bell event
1. Tap **+ Add Event**
2. Choose the **Day** (Mon, Tue, etc.)
3. Enter the **Hour** (use 24-hour format — e.g. 1 PM = 13)
4. Enter the **Minute**
5. Enter the **Track number** (1, 2, 3... matches your MP3 files)
6. Tap **Save Event**

### Step 4 — Repeat for all your bell times
Add one event for each bell ring you need throughout the week.

> 💡 The dashboard shows a **live countdown** to the next bell.

---

## 🕐 Setting the Correct Time

### Using the web dashboard (easiest)
1. Connect to `BellDashboard` Wi-Fi
2. Open `http://192.168.4.1`
3. Click **Set RTC Time**
4. Enter the correct date and time
5. Click **Apply Time**

### Using the buttons
1. Press **OK** (White) to open the menu
2. Press **UP/DOWN** to highlight **Set Time** → press **OK**
3. Adjust the **Hour** with UP/DOWN → press **OK**
4. Adjust the **Minute** with UP/DOWN → press **OK**
5. Done — time is saved automatically

---

## 🔊 Adjusting the Volume

### Using the web dashboard
- Open `http://192.168.4.1`
- Drag the **Volume slider** on the left side

### Using the buttons
1. Press **OK** → go to **Volume** → press **OK**
2. Press **UP** to increase, **DOWN** to decrease
3. Press **BACK** when done

---

## 🧪 Testing the Bell

- Press the **YELLOW (TEST)** button at any time
- OR open the dashboard and click **▶ Test Bell**

The bell will ring once using Track 1.

---

## 🗑️ Deleting a Bell Event

1. Open `http://192.168.4.1` on your phone
2. Find the event in the schedule table
3. Tap the **🗑️ trash icon** on that row
4. It's deleted immediately

---

## 💡 Everyday Tips

| Situation | What to do |
|-----------|-----------|
| Need to add a bell | Open dashboard → Add Event |
| Bell rang at wrong time | Delete old event → Add correct one |
| No sound from bell | Check AUX cable is plugged in to PA |
| Clock shows wrong time | Set RTC Time from dashboard |
| Forgot the Wi-Fi password | It's always `12345678` |
| Device frozen | Unplug and replug the power |

---

## ⚠️ Common Problems

**OLED shows `DFPlayer: FAIL`**
→ Check that the SD card is inserted properly into the DFPlayer slot

**Bell schedule shows 0 events**
→ You need to add events through the dashboard first

**Can't connect to BellDashboard Wi-Fi**
→ Make sure the device is powered on and the OLED is running

**Time is wrong after power cut**
→ The battery inside the RTC module may need replacing (CR2032)

**No sound even though bell triggers**
→ Check the AUX cable connection to the PA system; make sure PA is on

---

## 📱 Dashboard at a Glance

```
┌─────────────────────────────────────┐
│  🔔 School Bell  │  CONNECTED       │
├───────────────┬─────────────────────┤
│  Live Clock   │  Schedule Table     │
│  12:34:05     │  MON  07:30  T1     │
│               │  MON  08:45  T2     │
│  Next Bell    │  TUE  07:30  T1     │
│  13:00 Mon    │  ...                │
│               ├─────────────────────┤
│  Volume  67%  │  [Test] [Add Event] │
└───────────────┴─────────────────────┘
```

Connect to **BellDashboard** Wi-Fi → open **192.168.4.1**

---

*For full technical details, see `README.md` and `WIRING.md`*
*For detailed instructions, see `MANUAL.md`*
