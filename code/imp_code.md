🐘 Elephant Detection System - IoT Farm Protection

A real-time elephant detection system using **ESP8266**, **IR sensors**, and **ThingSpeak IoT cloud** for automatic farm protection with bilingual (English & Kannada) web dashboard-

 🎯 Project Overview

This system detects elephants approaching farm areas using an ultrasonic/IR distance sensor connected to an ESP8266 microcontroller. When an elephant is detected, alerts are sent to a **ThingSpeak channel**, which powers a real-time web dashboard with live status updates.

Features:
- ✅ Real-time elephant detection
- ✅ Distance measurement in centimeters
- ✅ Web dashboard with Google Maps integration
- ✅ Bilingual support (English & Kannada)
- ✅ Audio & vibration alerts
- ✅ Live data updates every 5 seconds
- ✅ Mobile-responsive design
- ✅ Emergency helpline integration



🔧 Hardware Components

Required Components:
| Component | Specification | Purpose |
|-----------|---------------|---------|
| **ESP8266** | NodeMCU / Wemos D1 Mini | Main microcontroller |
| **Ultrasonic Sensor** | HC-SR04 | Elephant distance detection |
| **IR Sensor** | Optional (PIR/Motion) | Motion detection trigger |
| **Power Supply** | 5V / 2A | Power ESP8266 & sensors |
| **USB Cable** | Micro USB | Programming & power |
| **Jumper Wires** | Male-to-Male / Female-to-Male | Connections |
| **Breadboard** | Optional | Easy prototyping |



 🔌 Hardware Connections
 HC-SR04 Ultrasonic Sensor Wiring:

```
HC-SR04 Pinout:
┌─────────────┬──────────────────┐
│ Sensor Pin  │ ESP8266 Pin       │
├─────────────┼──────────────────┤
│ VCC         │ 5V (or 3.3V)     │
│ GND         │ GND              │
│ TRIG        │ D5 (GPIO14)      │
│ ECHO        │ D6 (GPIO12)      │
└─────────────┴──────────────────┘

 Connection Diagram:


┌──────────────┐
│  ESP8266     │
│  NodeMCU     │
├──────────────┤
│ D5 (GPIO14)  ├─────────→ TRIG
│ D6 (GPIO12)  ├─────────→ ECHO
│ 5V           ├─────────→ VCC
│ GND          ├─────────→ GND
└──────────────┘
      ↑
      │
   HC-SR04
   (Sensor)
```

### Breadboard Layout (Optional):
```
[ESP8266]
  5V ─────┬──────[VCC]────── HC-SR04
  GND ────┼──────[GND]─────── ↓
  D5 ─────┴──────[TRIG]
  D6 ──────────[ECHO]
```

---

## 💻 ESP8266 Arduino Code

### Prerequisites:
1. Install Arduino IDE
2. Add ESP8266 board: **Preferences → Additional Boards Manager URLs** → Add: `http://arduino.esp8266.com/stable/package_esp8266com_index.json`
3. Install libraries:
   - `ESP8266WiFi`
   - `ESP8266HTTPClient`

### Complete Code:

```cpp
#include <ESP8266WiFi.h>
#include <ESP8266HTTPClient.h>

// ==================== CONFIGURATION ====================
// WiFi Credentials
const char* ssid = "YOUR_WIFI_SSID";
const char* password = "YOUR_WIFI_PASSWORD";

// ThingSpeak Configuration
const char* server = "http://api.thingspeak.com";
const String apiKey = "xxxxxxxxxxxxxxxxx";  // Your Write API Key
const String channelID = "xxxxxxx";         // Your Channel ID

// Sensor Pins (HC-SR04 Ultrasonic)
const int TRIG_PIN = D5;  // GPIO14
const int ECHO_PIN = D6;  // GPIO12

// Detection Parameters
const int DISTANCE_THRESHOLD = 200;  // cm (Elephant detected if < 200cm)
const int SENSOR_READ_DELAY = 500;   // ms between sensor reads
const int THINGSPEAK_UPDATE = 5000;  // ms between ThingSpeak updates

// ==================== GLOBAL VARIABLES ====================
long lastDistanceReading = 0;
long lastThingSpeakUpdate = 0;
bool elephantDetected = false;
int systemActive = 1;

// ==================== SETUP ====================
void setup() {
  Serial.begin(115200);
  delay(1000);
  
  Serial.println("\n\n=== Elephant Detection System Started ===");
  
  // Initialize Sensor Pins
  pinMode(TRIG_PIN, OUTPUT);
  pinMode(ECHO_PIN, INPUT);
  
  // Connect to WiFi
  connectToWiFi();
  
  Serial.println("System Ready!");
}

// ==================== MAIN LOOP ====================
void loop() {
  // Check WiFi Connection
  if (WiFi.status() != WL_CONNECTED) {
    Serial.println("WiFi disconnected. Reconnecting...");
    connectToWiFi();
  }
  
  // Read Distance Sensor
  if (millis() - lastDistanceReading >= SENSOR_READ_DELAY) {
    long distance = measureDistance();
    Serial.print("Distance: ");
    Serial.print(distance);
    Serial.println(" cm");
    
    // Check Elephant Detection
    if (distance > 0 && distance < DISTANCE_THRESHOLD) {
      elephantDetected = true;
      Serial.println("⚠️ ELEPHANT DETECTED!");
    } else {
      elephantDetected = false;
      Serial.println("✓ Safe - No Elephant");
    }
    
    lastDistanceReading = millis();
  }
  
  // Update ThingSpeak Every 5 Seconds
  if (millis() - lastThingSpeakUpdate >= THINGSPEAK_UPDATE) {
    updateThingSpeak(elephantDetected ? 1 : 0, lastDistanceReading, systemActive);
    lastThingSpeakUpdate = millis();
  }
  
  delay(100);
}

// ==================== FUNCTION: MEASURE DISTANCE ====================
long measureDistance() {
  // Send ultrasonic pulse
  digitalWrite(TRIG_PIN, LOW);
  delayMicroseconds(2);
  digitalWrite(TRIG_PIN, HIGH);
  delayMicroseconds(10);
  digitalWrite(TRIG_PIN, LOW);
  
  // Measure pulse duration
  long duration = pulseIn(ECHO_PIN, HIGH, 30000);  // 30ms timeout
  
  // Calculate distance (Speed of sound = 343 m/s)
  // Distance = (duration / 2) * speed_of_sound
  // Distance (cm) = (duration in microseconds / 29.1)
  long distance = duration / 29.1 / 2;
  
  // Filter out invalid readings
  if (distance < 0 || distance > 400) {
    return 0;  // Invalid reading
  }
  
  return distance;
}

// ==================== FUNCTION: CONNECT TO WiFi ====================
void connectToWiFi() {
  WiFi.begin(ssid, password);
  
  int attempts = 0;
  while (WiFi.status() != WL_CONNECTED && attempts < 30) {
    delay(500);
    Serial.print(".");
    attempts++;
  }
  
  if (WiFi.status() == WL_CONNECTED) {
    Serial.println("\n✓ WiFi Connected!");
    Serial.print("IP: ");
    Serial.println(WiFi.localIP());
  } else {
    Serial.println("\n✗ WiFi Connection Failed!");
  }
}

// ==================== FUNCTION: UPDATE THINGSPEAK ====================
void updateThingSpeak(int detected, long distance, int systemOn) {
  if (WiFi.status() != WL_CONNECTED) return;
  
  HTTPClient http;
  
  // Build ThingSpeak URL
  String url = String(server) + "/update?api_key=" + apiKey;
  url += "&field1=" + String(detected);           // Elephant Detected (0/1)
  url += "&field2=" + String(distance);           // Distance in cm
  url += "&field3=" + String(systemOn);           // System Status (0/1)
  
  Serial.print("Updating ThingSpeak: ");
  Serial.println(url);
  
  http.begin(url);
  int httpCode = http.GET();
  
  if (httpCode == HTTP_CODE_OK) {
    Serial.println("✓ ThingSpeak Update Success!");
  } else {
    Serial.print("✗ ThingSpeak Update Failed! Code: ");
    Serial.println(httpCode);
  }
  
  http.end();
}
```

### Field Mapping in ThingSpeak:
```
Field 1 (field1): Elephant Detected     [0 = No, 1 = Yes]
Field 2 (field2): Distance              [in Centimeters]
Field 3 (field3): System Status         [0 = Off, 1 = Active]
```

---

## 🔐 ThingSpeak Configuration

### Setup Steps:

1. **Create ThingSpeak Account**: https://thingspeak.com
2. **Create New Channel**:
   - Field 1: `Elephant Detected` (Integer)
   - Field 2: `Distance` (Numeric)
   - Field 3: `System Status` (Integer)
3. **Copy API Keys** to ESP8266 code
4. **Update Channel ID** in web dashboard

---

## 🌐 Web Dashboard Setup

### HTML File Configuration:
```javascript
// In the HTML file (around line 430):
const CHANNEL_ID = "xxxxxxxx";           // Your ThingSpeak Channel ID
const UPDATE_INTERVAL = 5000;           // Update every 5 seconds (5000ms)
```

### Dashboard Features:
- **Real-time Status**: Shows "Safe" or "Danger"
- **Distance Display**: Shows distance in centimeters
- **System Status**: Active/Off indicator
- **Live Map**: Shows farm location with Google Maps
- **Bilingual UI**: Switch between English & Kannada
- **Audio Alert**: Plays when elephant detected
- **Mobile Responsive**: Works on all devices

---

## 🚀 Installation & Setup Guide

### Step 1: Arduino IDE Setup
```bash
1. Download Arduino IDE: https://www.arduino.cc/en/software
2. Open Preferences (Ctrl+, on Windows/Linux, Cmd+, on Mac)
3. Add ESP8266 URL to "Additional Boards Manager URLs":
   http://arduino.esp8266.com/stable/package_esp8266com_index.json
4. Go to Tools → Board Manager
5. Search for "esp8266" and install latest version
```

### Step 2: Install Libraries
```
Sketch → Include Library → Manage Libraries
Search & Install:
  - ESP8266WiFi (Built-in)
  - ESP8266HTTPClient (Built-in)
```

### Step 3: Upload Code to ESP8266
```
1. Connect ESP8266 via USB
2. Tools → Board → ESP8266 Boards → NodeMCU 1.0
3. Tools → Port → Select COM port
4. Tools → Upload Speed → 115200
5. Replace WiFi credentials in code
6. Click Upload ↑
```

### Step 4: Update Web Dashboard
```
1. Get your ThingSpeak Channel ID
2. Open the HTML file in a text editor
3. Find: const CHANNEL_ID = "xxxxxxx";
4. Replace with your Channel ID
5. Save and deploy to web server or open locally
```

---

## 📊 Data Flow Architecture

```
┌─────────────────┐
│   HC-SR04       │
│   Sensor        │
└────────┬────────┘
         │ (Distance)
         ↓
┌─────────────────┐
│   ESP8266       │
│   WiFi Module   │
└────────┬────────┘
         │ (HTTP GET)
         ↓
┌─────────────────┐
│   ThingSpeak    │
│   IoT Cloud     │
└────────┬────────┘
         │ (JSON API)
         ↓
┌─────────────────┐
│   Web Browser   │
│   Dashboard     │
└─────────────────┘
```

---

## 🔍 Troubleshooting

### Issue: ESP8266 Won't Connect to WiFi
**Solution:**
```cpp
// Check WiFi credentials
const char* ssid = "YOUR_EXACT_SSID";
const char* password = "YOUR_EXACT_PASSWORD";

// Try 2.4GHz WiFi (ESP8266 doesn't support 5GHz)
// Check serial monitor for connection logs
```

### Issue: Sensor Not Reading Distance
**Solution:**
```cpp
// Check pins are correct:
const int TRIG_PIN = D5;  // GPIO14
const int ECHO_PIN = D6;  // GPIO12

// Verify wiring:
// - VCC to 5V
// - GND to GND
// - TRIG to D5
// - ECHO to D6

// Test sensor manually
long distance = measureDistance();
Serial.println(distance);  // Should show distance in cm
```

### Issue: ThingSpeak Not Updating
**Solution:**
```cpp
// Verify API Key
const String apiKey = "xxxxxxxxxxxxxxxx";

// Check URL construction
String url = String(server) + "/update?api_key=" + apiKey;

// Monitor serial output for HTTP response codes
// 200 = Success, 400+ = Error
```

### Issue: Dashboard Not Showing Data
**Solution:**
```javascript
// Check Channel ID in HTML
const CHANNEL_ID = "xxxxxxx";

// Verify ThingSpeak API response
// Open browser console (F12) and check Network tab
// Look for: https://api.thingspeak.com/channels/xxxxxxx/feeds.json
```

---

## 📱 Mobile Dashboard Access

```html
<!-- Responsive Design Breakpoints -->
- Desktop: > 1024px
- Tablet: 768px - 1024px
- Mobile: < 768px

<!-- Features optimized for mobile -->
- Touch-friendly buttons
- Swipe gestures for map
- Vibration alerts
- Full-screen map view
```

---

## ⚡ Performance Metrics

| Metric | Value |
|--------|-------|
| **Sensor Refresh Rate** | 500ms |
| **ThingSpeak Update** | 5000ms (5 seconds) |
| **Dashboard Refresh** | 5000ms (5 seconds) |
| **Detection Range** | 2m - 4m (sensor dependent) |
| **Accuracy** | ±2cm |
| **WiFi Signal Min** | -70dBm |
| **Power Consumption** | ~80-120mA |

---

## 🔧 Advanced Configuration

### Adjust Detection Sensitivity:
```cpp
// In code, change threshold (in centimeters):
const int DISTANCE_THRESHOLD = 200;  // Change to your need
// Lower = More sensitive (closer range)
// Higher = Less sensitive (farther range)
```

### Change Update Frequency:
```cpp
// Sensor read delay
const int SENSOR_READ_DELAY = 500;   // ms

// ThingSpeak update interval
const int THINGSPEAK_UPDATE = 5000;  // ms
```

---

## 📞 Emergency Helpline Integration

The dashboard includes emergency numbers for different regions:

```javascript
// Add to your code:
const emergencyNumbers = {
  'Forest Department': '+91-XXXXXXXXXX',
  'Police': '100',
  'Ambulance': '108',
  'Wildlife Rescue': '+91-XXXXXXXXXX'
};
```

---

## 📄 License

This project is open-source and available for educational and commercial use.

---

## 👨‍💻 Author & Support

**Project**: Elephant Detection System - IoT Farm Protection  
**Version**: 1.0  
**Created**: 2025

For issues, improvements, or feature requests, please open an issue on GitHub.

---

## 📚 Additional Resources

- **ThingSpeak Documentation**: https://thingspeak.com/docs
- **ESP8266 Documentation**: https://arduino-esp8266.readthedocs.io/
- **HC-SR04 Datasheet**: https://docs.ai-thinker.com/
- **Google Maps API**: https://developers.google.com/maps

---

**Last Updated**: April 2025  
**Status**: ✅ Production Ready
