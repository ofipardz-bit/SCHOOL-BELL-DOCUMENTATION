# Wiring Reference — School Bell System

## Complete Pin Map Table

### ESP32-S3 N16R8 GPIO Assignments

| GPIO | Direction | Connected To | Notes |
|------|-----------|-------------|-------|
| GPIO 2  | INPUT_PULLUP | TEST Button → GND | Manual bell trigger |
| GPIO 4  | INPUT_PULLUP | BACK Button → GND | Menu back |
| GPIO 5  | INPUT_PULLUP | OK Button → GND | Menu confirm |
| GPIO 7  | INPUT_PULLUP | DOWN Button → GND | Menu scroll down |
| GPIO 8  | I²C SDA | OLED SDA + RTC SDA | Shared I²C bus |
| GPIO 9  | I²C SCL | OLED SCL + RTC SCL | Shared I²C bus |
| GPIO 15 | INPUT_PULLUP | UP Button → GND | Menu scroll up |
| GPIO 16 | UART1 RX | DFPlayer TX | HardwareSerial(1) |
| GPIO 17 | UART1 TX | DFPlayer RX via 1kΩ | Resistor protects DFPlayer |
| EN      | — | Reset Button → GND | Hardware reset |

---

## Component Wiring Details

### SSD1306 OLED (0.96", I²C)

```
OLED Pin    →   ESP32-S3
─────────────────────────
VCC         →   3.3V
GND         →   GND
SDA         →   GPIO 8
SCL         →   GPIO 9
```

I²C Address: `0x3C`

---

### DS3231 RTC Module

```
RTC Pin     →   ESP32-S3
─────────────────────────
VCC         →   3.3V
GND         →   GND
SDA         →   GPIO 8  (shared with OLED)
SCL         →   GPIO 9  (shared with OLED)
```

I²C Address: `0x68`  
> Ensure CR2032 battery is installed for time retention during power loss.

---

### DFPlayer Mini

```
DFPlayer Pin  →   Connection
──────────────────────────────────────────
VCC           →   5V
GND           →   GND
RX            →   [1kΩ resistor] → GPIO 17
TX            →   GPIO 16
DAC_L         →   AUX Jack TIP (Left ch.) [optional: 10µF cap in series]
DAC_R         →   AUX Jack RING (Right ch.) [optional: 10µF cap in series]
GND           →   AUX Jack GND
```

> ⚠️ SPK1 / SPK2 are NOT connected. Audio goes via DAC_L/DAC_R to AUX jack.  
> The AUX jack connects to a PA/amplifier system via 3.5mm cable.

---

### Push Buttons (all 5)

All buttons are wired identically:

```
Button Pin 1  →   GPIO (see table above)
Button Pin 2  →   GND
```

Firmware uses `INPUT_PULLUP`. No external resistors needed.

```
GPIO ──┤Button├── GND
```

---

### 3.5mm AUX Output Jack

```
AUX Jack Pin  →   DFPlayer Pin
─────────────────────────────────────────────────────
TIP  (Left)   →   DAC_L  (optionally through 10µF cap)
RING (Right)  →   DAC_R  (optionally through 10µF cap)
SLEEVE (GND)  →   GND (common ground)
```

> Connect to PA system amplifier input via standard 3.5mm aux cable.

---

### AUX Capacitor Filter (Optional but Recommended)

To eliminate DC offset pop when audio starts/stops:

```
DFPlayer DAC_L ──[10µF+]──[10µF-]── AUX TIP (Left)
DFPlayer DAC_R ──[10µF+]──[10µF-]── AUX RING (Right)
```

Use 10µF **electrolytic** capacitors, positive side toward DFPlayer.

---

### Thread Expansion Header (1×6 Male)

```
Pin 1: 3.3V         ← from ESP32 3V3 rail
Pin 2: GND          ← common ground
Pin 3: UART_TX      ← ESP32-S3 TX (to future H2 RX)
Pin 4: UART_RX      ← ESP32-S3 RX (from future H2 TX)
Pin 5: I2C_SDA      ← GPIO 8 (shared bus)
Pin 6: I2C_SCL      ← GPIO 9 (shared bus)
```

---

## Power Distribution

```
5V Power Adapter
│
├── ESP32-S3 (5V via USB or VIN)
│   └── 3.3V LDO (internal) → OLED, RTC, Buttons
│
└── DFPlayer Mini (5V direct)
    └── DAC_L/DAC_R → AUX Jack → PA System
```

---

## I²C Device Addresses

| Device | I²C Address |
|--------|------------|
| SSD1306 OLED | `0x3C` |
| DS3231 RTC | `0x68` |

Both devices share GPIO8 (SDA) and GPIO9 (SCL).

---

## Quick-Check Troubleshooting

| Symptom | Likely Cause | Fix |
|---------|-------------|-----|
| OLED blank | `setTextColor(WHITE)` missing | Already fixed in firmware |
| OLED text cut off | Font size too large | Use `setTextSize(1)` |
| No audio from AUX | SPK terminals used instead of DAC | Rewire to DAC_L/DAC_R |
| DFPlayer not responding | Missing 1kΩ on RX | Add resistor |
| RTC time wrong after power loss | No CR2032 battery | Install battery |
| Random resets | Insufficient 5V current | Use 5V 2A adapter |
| DC pop on audio start | No coupling caps | Add 10µF caps on DAC lines |
