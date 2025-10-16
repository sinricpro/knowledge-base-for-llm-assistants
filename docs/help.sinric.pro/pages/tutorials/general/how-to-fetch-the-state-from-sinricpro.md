
# How to fetch the device state from SinricPro

SinricPro is an event-driven IoT platform — meaning it pushes state changes (like ON/OFF) to your device via callbacks when users interact with devices through Alexa, Google Home, or the SinricPro dashboard.

However, you may need to **fetch or restore the current switch state** — for example, during device boot or after a reboot to sync with the cloud.

---

## Solution: Use `restoreDeviceStates()` to Fetch Switch State

The SinricPro SDK (for ESP8266/ESP32/Arduino) provides a built-in method to restore the last known device states from the SinricPro server.

---

## Step-by-Step Guide

### 1. Enable State Restore in `setup()`

After initializing SinricPro, enable state restoration:

```cpp
SinricPro.restoreDeviceStates(true); // Fetch latest device states from server on startup
```

This triggers your device’s callback functions with the last known state after connecting to SinricPro.

---

### 2. Handle State in `onPowerState` Callback

Define a callback to handle incoming power state events — including those restored from the cloud:

```cpp
bool onPowerState(const String &deviceId, bool &state) {
  Serial.printf("Device %s turned %s\r\n", deviceId.c_str(), state ? "on" : "off");

  // Example: Update physical relay or LED
  digitalWrite(RELAY_PIN, state ? HIGH : LOW);

  return true; // Confirm successful handling
}
```

> 💡 This callback is triggered:
> - When a user toggles the switch via app/voice.
> - When `restoreDeviceStates(true)` fetches the cloud state.
> - On reconnection if state restore is enabled.

---

### 3. Full Working Example (ESP8266/ESP32)

```cpp
#include <Arduino.h>
#include <SinricPro.h>
#include <SinricProSwitch.h>

#define WIFI_SSID         "your-wifi-ssid"
#define WIFI_PASS         "your-wifi-password"
#define APP_KEY           "your-app-key"
#define APP_SECRET        "your-app-secret"
#define SWITCH_ID         "your-device-id"
#define BAUD_RATE         115200

#define RELAY_PIN         12  // Adjust to your GPIO

// Power state callback
bool onPowerState(const String &deviceId, bool &state) {
  Serial.printf("Device %s turned %s\r\n", deviceId.c_str(), state ? "on" : "off");
  digitalWrite(RELAY_PIN, state ? HIGH : LOW); // Update hardware
  return true;
}

void setup() {
  pinMode(RELAY_PIN, OUTPUT);
  digitalWrite(RELAY_PIN, LOW); // Initialize relay OFF

  Serial.begin(BAUD_RATE);
  WiFi.begin(WIFI_SSID, WIFI_PASS);

  // Wait for WiFi
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("Connected to WiFi!");

  // Register device and callback
  SinricProSwitch& mySwitch = SinricPro[SWITCH_ID];
  mySwitch.onPowerState(onPowerState);

  // Enable cloud state sync on boot
  SinricPro.restoreDeviceStates(true);

  // Start SinricPro
  SinricPro.begin(APP_KEY, APP_SECRET);
}

void loop() {
  SinricPro.handle(); // Must be called in loop
}
```

---

## Fetch Device State via REST API

You can fetch a device’s current state using SinricPro’s API via `curl`. This requires an API Key from SinricPro Portal -> Credentials

---

### Step 1: Authenticate and Get Access Token

```bash
curl -X POST 'https://api.sinric.pro/api/v1/auth' \
  --header 'x-sinric-api-key: a614xxxx-xxxx-xxxx-xxxx-xxxxxxxx'
```

> Replace `a614xxxx-xxxx-xxxx-xxxx-xxxxxxxx` with your actual **App Key** from the SinricPro portal.

#### Example Response:

```json
{
  "success": true,
  "message": "OK.",
  "accessToken": "eyJhbG.xxxxxxxxx.xxxxxxxxxxxxxxx...",
  "refreshToken": "9i4GV2Llpsl87FoT1HvQcxaybP3xxxxxxxxxxxxxxxx..",
  "expiresIn": 604800
}
```

---

### Step 2: Fetch Device State

Use the `accessToken` to get the device’s current state:

```bash
curl -X GET 'https://api.sinric.pro/api/v1/devices/{device_id}' \
  --header 'Authorization: Bearer eyJhbG.xxxxxxxxx.xxxxxxxxxxxxxxx...'
```

> Replace:
> - `{device_id}` with your actual device ID (e.g., `60a8b1c23d9f7f000123abcd`)
> - `eyJhbG...` with your actual `accessToken`

#### ✅ Example Response:

```json
{
  "deviceId": "60a8b1c23d9f7f000123abcd",
  "deviceType": "SWITCH",
  "name": "Living Room Light",
  "description": "",
}
```

### Alternative: Store State Locally
Save the latest state to EEPROM, SPIFFS, or Flash inside the `onPowerState` callback:

```cpp
void saveStateToFlash(bool state) {
  // Example: Save to EEPROM or Preferences (ESP32)
  // Implement your preferred non-volatile storage here
}
```

Then read it on boot *before* or *while waiting* for `restoreDeviceStates()` to complete.

---

## Pro Tip

Always initialize your hardware to a safe/default state (e.g., OFF), then let `restoreDeviceStates(true)` sync it with the cloud. This ensures your physical device matches what the user expects — even after a power cycle.