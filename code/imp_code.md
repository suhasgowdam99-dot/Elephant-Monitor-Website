
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
