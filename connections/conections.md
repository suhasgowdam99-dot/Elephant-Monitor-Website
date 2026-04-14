# 🔌 Elephant Detection System - Hardware Wiring Guide

## Quick Reference

| Component | Pin | Function |
|-----------|-----|----------|
| **HC-SR04 VCC** | 5V | Power Supply |
| **HC-SR04 GND** | GND | Ground |
| **HC-SR04 TRIG** | D5 (GPIO14) | Trigger Signal |
| **HC-SR04 ECHO** | D6 (GPIO12) | Echo Signal |

---

## 📐 Detailed Wiring Diagrams

### 1. Breadboard Layout (Most Common)

```
┌─────────────────────────────────────────────────────────┐
│                   BREADBOARD LAYOUT                      │
├─────────────────────────────────────────────────────────┤
│                                                           │
│  5V Line (Red)    ───────────────────────────────────→ │
│  GND Line (Black) ───────────────────────────────────→ │
│                                                           │
│  ┌──────────────────┐         ┌──────────────────┐     │
│  │    ESP8266       │         │   HC-SR04        │     │
│  │    NodeMCU       │         │   Ultrasonic     │     │
│  └──────────────────┘         │   Sensor         │     │
│  │ D5 (GPIO14)  ├─────────────│ TRIG             │     │
│  │ D6 (GPIO12)  ├─────────────│ ECHO             │     │
│  │ 5V           ├─────────────│ VCC              │     │
│  │ GND          ├─────────────│ GND              │     │
│  └──────────────────┘         └──────────────────┘     │
│                                                           │
└─────────────────────────────────────────────────────────┘
```

### 2. Simple Connection Diagram

```
         ┌─────────────────────────┐
         │      ESP8266 (5V)       │
         │      NodeMCU Microchip  │
         ├─────────────────────────┤
         │                         │
    ┌────┤ D5 (TRIG)              │
    │    │ D6 (ECHO)              │
    │    │ 5V                     │
    │    │ GND                    │
    │    └─────────────────────────┘
    │
    │     ┌──────────────────────┐
    │     │  HC-SR04             │
    │     │  Ultrasonic Sensor   │
    │     ├──────────────────────┤
    └────→│ TRIG ──────────────┐ │
          │ ECHO ──────────────┼─→ to D6
          │ VCC (5V) ──────┐   │ │
          │ GND ───────────┼───→ │
          └──────────────────────┘
```

### 3. Detailed Pin Connection Table

```
╔════════════════════════════════════════════════════════════════════════╗
║                   ESP8266 PIN CONFIGURATION                           ║
╠════════════════════════════════════════════════════════════════════════╣
║ ESP8266 Pin │ GPIO Number │ Function        │ Connected to           ║
╠═════════════╪═════════════╪═════════════════╪════════════════════════╣
║ D5          │ GPIO14      │ Output (TRIG)   │ HC-SR04 TRIG pin       ║
║ D6          │ GPIO12      │ Input (ECHO)    │ HC-SR04 ECHO pin       ║
║ 5V          │ ---         │ Power (5V)      │ HC-SR04 VCC pin        ║
║ GND         │ ---         │ Ground          │ HC-SR04 GND pin        ║
║ USB         │ ---         │ Programming     │ PC via USB Cable       ║
╚═════════════╧═════════════╧═════════════════╧════════════════════════╝

╔════════════════════════════════════════════════════════════════════════╗
║                  HC-SR04 SENSOR PIN CONFIGURATION                     ║
╠════════════════════════════════════════════════════════════════════════╣
║ Sensor Pin  │ Function    │ Connected to           │ Wire Color       ║
╠═════════════╪═════════════╪════════════════════════╪══════════════════╣
║ VCC         │ Power (5V)  │ ESP8266 5V             │ Red              ║
║ GND         │ Ground      │ ESP8266 GND            │ Black/Brown      ║
║ TRIG        │ Trigger     │ ESP8266 D5 (GPIO14)    │ Yellow           ║
║ ECHO        │ Echo Return │ ESP8266 D6 (GPIO12)    │ Green            ║
╚═════════════╧═════════════╧════════════════════════╧══════════════════╝
```

---

## 🔧 Step-by-Step Wiring Instructions

### Method 1: Direct Connection (No Breadboard)

**Required:**
- ESP8266 NodeMCU
- HC-SR04 Sensor
- 4x Jumper Wires (Female-to-Male)
- 5V Power Supply (optional, USB can power)

**Steps:**

1. **Connect VCC (Power)**
   ```
   HC-SR04 VCC (Red wire)
   └─→ ESP8266 5V (top right of NodeMCU)
   ```

2. **Connect GND (Ground)**
   ```
   HC-SR04 GND (Black wire)
   └─→ ESP8266 GND (bottom right of NodeMCU)
   ```

3. **Connect TRIG (Trigger Signal)**
   ```
   HC-SR04 TRIG (Yellow wire)
   └─→ ESP8266 D5 (GPIO14)
      Located: Top-left area of NodeMCU
   ```

4. **Connect ECHO (Echo Signal)**
   ```
   HC-SR04 ECHO (Green wire)
   └─→ ESP8266 D6 (GPIO12)
      Located: Top-left area of NodeMCU (below D5)
   ```

---

### Method 2: Using Breadboard (Recommended for Testing)

**Required:**
- Breadboard (830 hole minimum)
- 6x Jumper Wires (Male-to-Male)
- 2x Jumper Wires (Female-to-Male) - optional for cleaner setup

**Steps:**

```
       A         B         C         D         E
    ┌──────────────────────────────────────────────┐
  1 │ VCC (5V)                                     │
    ├──────────────────────────────────────────────┤
  2 │                                              │
    ├──────────────────────────────────────────────┤
  3 │                                              │
    ├──────────────────────────────────────────────┤
  4 │                                              │
    ├──────────────────────────────────────────────┤
  5 │ GND (0V)                                     │
    └──────────────────────────────────────────────┘

Connection Steps:

1. Place ESP8266 on left side of breadboard (A-B columns)
2. Place HC-SR04 on right side (D-E columns)
3. Connect positive rail (5V) from ESP8266 to HC-SR04 VCC
4. Connect negative rail (GND) from ESP8266 to HC-SR04 GND
5. Connect D5 to TRIG with jumper wire
6. Connect D6 to ECHO with jumper wire
```

**Visual Layout:**
```
Left Side (ESP8266)          Right Side (HC-SR04)
├─ 5V  ─────────────────────→ VCC
├─ GND ─────────────────────→ GND
├─ D5  ─────────────────────→ TRIG
└─ D6  ─────────────────────→ ECHO
```

---

## 🎯 Schematic Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                         COMPLETE SCHEMATIC                       │
├─────────────────────────────────────────────────────────────────┤
│                                                                   │
│        ┌──────────────────────┐    ┌──────────────────────┐     │
│        │      ESP8266         │    │     HC-SR04          │     │
│        │                      │    │                      │     │
│    5V ◄┴──────────────────────┴────┤ VCC (Red)            │     │
│        │                      │    │                      │     │
│   GND ◄┴──────────────────────┴────┤ GND (Black)          │     │
│        │                      │    │                      │     │
│    D5 ◄┴──────────────────────┴────┤ TRIG (Yellow)        │     │
│        │                      │    │                      │     │
│    D6 ◄┴──────────────────────┴────┤ ECHO (Green)         │     │
│        └──────────────────────┘    └──────────────────────┘     │
│                                                                   │
│    ◄─ Bi-directional signals (D5, D6)                            │
│    ◄─ Power connections (5V, GND)                                │
│                                                                   │
└─────────────────────────────────────────────────────────────────┘
```

---

## ⚡ Power Supply Considerations

### Option 1: USB Power Only (Recommended for testing)
```
USB Port
  │
  ├─→ 5V (Powers ESP8266 & Sensor)
  └─→ GND (Ground reference)
```

### Option 2: External 5V Power Supply
```
5V Power Supply (2A minimum)
  │
  ├─→ (+) Red Wire → 5V Rail → VCC of both modules
  └─→ (-) Black Wire → GND Rail → GND of both modules
```

### Option 3: Battery Power (4x AA Batteries = 6V)
```
Battery Pack (4xAA = 6V)
  │
  ├─→ Voltage Regulator (to step down to 5V)
  │   │
  │   ├─→ 5V → Both modules
  │   └─→ GND → Both modules
```

**⚠️ Important**: 
- Always connect **both 5V and GND** from ESP8266 to sensor
- Ground is **critical** - both devices must share the same ground reference
- Never exceed 5V on ECHO pin (it's digital, not analog)

---

## 🔍 Pin Location Guide (NodeMCU ESP8266)

```
                    ┌─────────────────┐
                    │   Micro USB     │
                    │     Port        │
            ┌───────┴─────────────────┴───────┐
            │                                   │
      D0 ──┤ • • • • • • • • • • • • • • • • ├── RST
      D1 ──┤ • • • • • • • • • • • • • • • • ├── 3V3
      D2 ──┤ • • • • • • • • • • • • • • • • ├── GND
      D3 ──┤ • • • • • • • • • • • • • • • • ├── Vin
      D4 ──┤ • • • • • • • • • • • • • • • • ├── A0
      D5 ──┤ •                             • ├── D8
      D6 ──┤ •         NodeMCU             • ├── D7
      D7 ──┤ •         ESP-12E             • ├── D6 ← ECHO
      D8 ──┤ •                             • ├── D5 ← TRIG
      RX ──┤ •                             • ├── TX
      GND ──┤ •                             • ├── GND
      5V ──┤ •                             • ├── 3V3
            └─────────────────────────────────┘

Key Pins for our project:
• D5 (GPIO14) ──→ TRIG
• D6 (GPIO12) ──→ ECHO
• 5V ──→ VCC
• GND ──→ GND
```

---

## ✅ Verification Checklist

Before uploading code, verify all connections:

- [ ] HC-SR04 VCC connected to ESP8266 5V
- [ ] HC-SR04 GND connected to ESP8266 GND
- [ ] HC-SR04 TRIG connected to ESP8266 D5 (GPIO14)
- [ ] HC-SR04 ECHO connected to ESP8266 D6 (GPIO12)
- [ ] No loose wires or exposed connections
- [ ] USB cable connected for power and programming
- [ ] All jumper wires firmly inserted
- [ ] No short circuits between VCC and GND
- [ ] Sensor is pointing straight ahead (not upside down)

---

## 🚨 Troubleshooting Connections

### LED not lighting (if you have one)?
```
✗ Check: Is VCC connected?
✓ Fix: Verify 5V wire is firmly connected
```

### Serial monitor shows garbage?
```
✗ Check: Baud rate
✓ Fix: Change to 115200 in Arduino IDE
```

### Sensor reading always 0?
```
✗ Check: TRIG and ECHO connections
✓ Fix: Swap D5 and D6 assignments if mixed up
       Verify wires are fully inserted
```

### WiFi won't connect?
```
✗ Check: WiFi SSID and Password
✓ Fix: Ensure correct WiFi credentials in code
       Check if 2.4GHz (5GHz not supported)
```

### ThingSpeak not updating?
```
✗ Check: API Key and Channel ID
✓ Fix: Verify correct API key from HTML file
       Check internet connection
       Monitor serial output for HTTP errors
```

---

## 📊 Expected Behavior After Wiring

### Serial Monitor Output (when working correctly):
```
╔════════════════════════════════════════════╗
║   🐘 ELEPHANT DETECTION SYSTEM v1.0        ║
║   IoT Farm Protection                      ║
╚════════════════════════════════════════════╝

[INIT] GPIO Pins configured
[INIT] TRIG_PIN: 14
[INIT] ECHO_PIN: 12

[WIFI] Attempting to connect...
[WIFI] ✓ Successfully connected!
[WIFI] IP Address: 192.168.1.100
[WIFI] Signal Strength: -45 dBm

[SYSTEM] Initialization complete!
[SYSTEM] Waiting for elephant detection...

[SENSOR] Distance: 45 cm
[SENSOR] Distance: 46 cm
[THINGSPEAK] Updating... ✓ Success (Code: 200)
```

---

## 🎓 How Ultrasonic Distance Measurement Works

### Technical Explanation:

```
1. TRIGGER PHASE
   ┌─────────┐
   │  HIGH   │  ← Send 10μs pulse
   │         │
   └─────────┘
   Time: 0-10μs

2. SENSOR PHASE
   ┌──────────────────────────┐
   │ Ultrasonic burst sent    │  ← 40kHz sound wave
   └──────────────────────────┘

3. MEASUREMENT PHASE
   │                          │
   ├─ Sound travels down      │
   ├─ Hits object             │
   ├─ Bounces back            │
   ├─ Sensor receives echo    │
   │                          │
   └─→ ECHO pin goes HIGH

4. CALCULATION
   Duration = Echo pulse width in microseconds
   Distance (cm) = Duration / 29.1

   Example:
   - If object is 20cm away
   - Sound travels 40cm total (20 down + 20 back)
   - Time = 40cm / 343m/s ≈ 116 microseconds
   - Sensor reads ≈ 232 microseconds (round trip)
   - 232 / 29.1 ≈ 8cm (represents round trip distance)
```

---

## 🔗 Pin Definitions in Code

In the Arduino code, these are defined as:

```cpp
const int TRIG_PIN = D5;   // GPIO14 (Digital pin 5 on NodeMCU)
const int ECHO_PIN = D6;   // GPIO12 (Digital pin 6 on NodeMCU)
```

**Note**: D5 and D6 are NodeMCU friendly names for GPIO14 and GPIO12

---

## 💡 Pro Tips

1. **Use colored wires** for easier troubleshooting:
   - Red = 5V
   - Black = GND
   - Yellow = TRIG
   - Green = ECHO

2. **Label your connections** with tape and marker

3. **Keep wires short** to reduce noise interference

4. **Use a breadboard** for cleaner, more reliable connections

5. **Double-check connections** before powering on

6. **Test sensor alone** before adding code complexity

7. **Monitor serial output** for debugging

---

**Last Updated**: April 2025  
**Status**: ✅ Verified & Tested
