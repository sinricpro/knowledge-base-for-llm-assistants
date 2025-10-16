---
title: ESP32 RF Transmitter + SinricPro Integration Tutorial
layout: post
lang: en
---
 
Control your RF devices (lights, fans, outlets) via Alexa, Google Home, or the SinricPro app — using your existing 433MHz RF transmitter!

## What You’ll Achieve

You’ll transform your ESP32 from a simple button-controlled RF remote into a **smart home hub** that can receive voice/app commands and transmit the same RF codes you’ve already captured.

---

## Prerequisites

- ESP32 Development Board
- 433MHz RF Transmitter Module
- Arduino IDE (or PlatformIO)
- WiFi Network
- SinricPro Account: [https://portal.sinric.pro](https://portal.sinric.pro)

---

## Hardware Setup

Connect your RF transmitter to the ESP32:

| RF Transmitter Pin | ESP32 GPIO |
|--------------------|------------|
| VCC                | 3.3V       |
| GND                | GND        |
| DATA               | GPIO 4     |

*(You can remove the buttons — they’re optional once SinricPro is active.)*

---

## Step 1: Install Required Libraries

In **Arduino IDE**:

1. Go to `Sketch → Include Library → Manage Libraries`
2. Install:
   - `RCSwitch` by Suat Özgür
   - `SinricPro` by SinricPro

---

## Step 2: Set Up Devices in SinricPro Portal

1. Go to [https://portal.sinric.pro](https://portal.sinric.pro)
2. Log in or create an account.
3. Click **“Add Device”** → Add:
   - A **Switch** for Lights → Note its `Device ID`
   - A **Switch** or **Fan** for Fan → Note its `Device ID`
4. Go to **“Account” → “Security”** → Copy your:
   - `APP KEY`
   - `APP SECRET`

---

## Step 3: Upload the Integration Code

Replace the placeholders with your credentials and RF codes.

```cpp
#include <Arduino.h>
#include <RCSwitch.h>
#include <WiFi.h>
#include <SinricPro.h>
#include <SinricProSwitch.h>

// ========================
// CONFIGURATION
// ========================

// WiFi Credentials
const char* WIFI_SSID = "YOUR_WIFI_SSID";
const char* WIFI_PASS = "YOUR_WIFI_PASSWORD";

// SinricPro Credentials (from https://portal.sinric.pro)
#define APP_KEY    "YOUR_APP_KEY_HERE"
#define APP_SECRET "YOUR_APP_SECRET_HERE"

// Device IDs (from SinricPro portal)
#define LIGHTS_ID  "YOUR_LIGHTS_DEVICE_ID"
#define FAN_ID     "YOUR_FAN_DEVICE_ID"

// RF Codes (your captured codes)
const unsigned long CODE_LIGHTS = 13651983;
const unsigned long CODE_HIGH   = 13651971;
const unsigned long CODE_OFF    = 13652160;

// Initialize RF Transmitter
RCSwitch mySwitch = RCSwitch();

// ========================
// CALLBACK FUNCTIONS
// ========================

// Handle Lights (ON/OFF)
bool onLights(const String &deviceId, bool &state) {
  if (state) {
    mySwitch.send(CODE_LIGHTS, 24);
    Serial.println("RF -> Lights ON");
  } else {
    mySwitch.send(CODE_OFF, 24);
    Serial.println("RF -> Lights OFF");
  }
  return true; // Command handled successfully
}

// Handle Fan (ON=High / OFF=Off)
bool onFan(const String &deviceId, bool &state) {
  if (state) {
    mySwitch.send(CODE_HIGH, 24);
    Serial.println("RF -> Fan HIGH");
  } else {
    mySwitch.send(CODE_OFF, 24);
    Serial.println("RF -> Fan OFF");
  }
  return true;
}

// ========================
// SETUP
// ========================

void setup() {
  Serial.begin(115200);

  // Initialize RF Transmitter on GPIO 4
  mySwitch.enableTransmit(4);
  mySwitch.setPulseLength(228); // Adjust if needed

  // Connect to WiFi
  Serial.print("Connecting to WiFi");
  WiFi.begin(WIFI_SSID, WIFI_PASS);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("\nWiFi Connected!");

  // Attach callbacks to SinricPro devices
  SinricProSwitch &lights = SinricPro[LIGHTS_ID];
  lights.onPowerState(onLights);

  SinricProSwitch &fan = SinricPro[FAN_ID];
  fan.onPowerState(onFan);

  // Start SinricPro
  SinricPro.begin(APP_KEY, APP_SECRET);
}

// ========================
// MAIN LOOP
// ========================

void loop() {
  SinricPro.handle(); // Keep connection alive & listen for commands
}
```

---

## Step 4: Test It!

1. Upload the code to your ESP32.
2. Open **Serial Monitor** (`Ctrl+Shift+M`) at 115200 baud.
3. Wait for “ WiFi Connected!” and “SinricPro Ready!”
4. Use Alexa / Google Assistant / SinricPro App:
   - *“Alexa, turn on the lights”*
   - *“Hey Google, turn off the fan”*

You should see confirmation in Serial Monitor and your RF device should respond!

---

## Bonus: Add Physical Buttons Back (Optional)

Want both voice AND button control? Just merge your original `loop()` logic:

```cpp
// GPIOs for buttons
const int BUTTON_LIGHTS = 12;
const int BUTTON_HIGH   = 13;
const int BUTTON_OFF    = 14;

void setup() {
  // ... [previous setup code]

  // Set buttons as INPUT_PULLUP
  pinMode(BUTTON_LIGHTS, INPUT_PULLUP);
  pinMode(BUTTON_HIGH, INPUT_PULLUP);
  pinMode(BUTTON_OFF, INPUT_PULLUP);
}

void loop() {
  SinricPro.handle(); // Must run frequently

  // Manual button control (active LOW)
  if (digitalRead(BUTTON_LIGHTS) == LOW) {
    mySwitch.send(CODE_LIGHTS, 24);
    Serial.println("Button: Lights ON");
    delay(500); // debounce
  }

  if (digitalRead(BUTTON_HIGH) == LOW) {
    mySwitch.send(CODE_HIGH, 24);
    Serial.println("Button: Fan HIGH");
    delay(500);
  }

  if (digitalRead(BUTTON_OFF) == LOW) {
    mySwitch.send(CODE_OFF, 24);
    Serial.println("Button: OFF");
    delay(500);
  }
}
```

---

## Advanced: Multi-Speed Fan?

If your fan has Low/Med/High, use `SinricProFanUS` instead of `SinricProSwitch`:

```cpp
#include <SinricProFanUS.h>

#define FAN_ID "YOUR_FAN_ID"

bool onFanSpeed(const String &deviceId, int &speed) {
  switch (speed) {
    case 0: mySwitch.send(CODE_OFF, 24); break;
    case 1: mySwitch.send(CODE_LOW, 24); break;
    case 2: mySwitch.send(CODE_MED, 24); break;
    case 3: mySwitch.send(CODE_HIGH, 24); break;
  }
  return true;
}

// In setup():
SinricProFanUS &fan = SinricPro[FAN_ID];
fan.onPowerState([](const String &, bool &state) {
    // Optional: handle simple on/off if needed
    return true;
});
fan.onPowerLevel(onFanSpeed);     // Handles speed levels
```

> You’ll need to define `CODE_LOW` and `CODE_MED` if your remote supports them.

---

## Troubleshooting Tips

- Double-check your RF codes and pulse length.
- Ensure WiFi credentials and SinricPro keys are correct.
- Device IDs must match exactly (copy-paste from portal).
- If RF range is poor, add a 17cm wire as antenna to transmitter.

---

## Done!

You’ve now bridged your legacy RF devices with modern smart assistants — no replacing hardware required!

Let me know if you want to add more devices, schedules, or local button feedback (LEDs/buzzer)!

--- 

**Next Step:** Try saying *“Alexa, discover devices”* — and enjoy voice-controlled RF gadgets!
```