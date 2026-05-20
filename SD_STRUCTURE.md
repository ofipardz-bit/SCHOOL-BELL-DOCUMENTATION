# SD Card Setup Guide — School Bell System

## Formatting

- Format the microSD card as **FAT32**
- Recommended capacity: 8 GB – 32 GB
- Insert into the DFPlayer Mini's microSD slot

---

## Required Folder & File Structure

```
microSD Card (FAT32)
└── 01/
    ├── 001.mp3     ← Track 1
    ├── 002.mp3     ← Track 2
    ├── 003.mp3     ← Track 3
    └── ...
```

> ⚠️ **Critical:** The DFPlayer Mini hardware **requires** numeric folder names (`01`, `02`, …) and numeric filenames (`001.mp3`, `002.mp3`, …). Any other naming will not play.

---

## Audio File Naming Convention

| Track # | Filename | Suggested Use |
|---------|----------|---------------|
| 1 | `001.mp3` | Class Start / Morning Bell |
| 2 | `002.mp3` | Break / Recess Bell |
| 3 | `003.mp3` | Dismissal Bell |
| 4 | `004.mp3` | Warning Bell |
| 5+ | `00N.mp3` | Custom use |

Place all audio files inside the `/01/` folder.

---

## Schedule Storage

The schedule is stored in **SPIFFS** (internal ESP32 flash), not on the SD card.

- Schedule file path: `/active.csv` (SPIFFS)
- Managed via the web dashboard at `http://192.168.4.1`
- A backup copy is automatically saved to SPIFFS

---

## Schedule CSV Format

```csv
DAY,TIME,TRACK
MON,07:30,1
MON,08:45,2
MON,10:00,3
MON,12:00,2
MON,15:30,3
TUE,07:30,1
TUE,08:45,2
WED,07:30,1
THU,07:30,1
FRI,07:30,1
FRI,15:30,3
```

### Field Definitions

| Field | Format | Valid Values |
|-------|--------|-------------|
| `DAY` | 3-letter abbreviation | MON / TUE / WED / THU / FRI / SAT / SUN |
| `TIME` | HH:MM (24-hour) | 00:00 – 23:59 |
| `TRACK` | Integer | 1 – 999 (matches `/01/00N.mp3`) |

### Rules

- First line must be the header: `DAY,TIME,TRACK`
- No spaces around commas
- Maximum 50 events per file (`MAX_EVENTS = 50`)
- Events can be in any order — firmware sorts by time internally

---

## Uploading the Schedule

1. Power on the device
2. Connect to Wi-Fi: **`BellDashboard`** / `12345678`
3. Open browser → `http://192.168.4.1`
4. Use the dashboard to add/edit/delete events
5. Click **Save** — schedule is written to SPIFFS immediately

Changes take effect without restarting the device.
