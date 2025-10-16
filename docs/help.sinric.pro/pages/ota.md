---
title: SinricPro OTA Update Tutorial for ESP32/ESP8266/RP2040
weight: 4
lang: en
youtubeId: FxtsQuC9mn8
---

This tutorial walks you through setting up **Over-The-Air (OTA) firmware updates** for your ESP32 or ESP8266 device using **SinricPro**. OTA allows you to remotely update your device’s firmware without physically connecting it to a computer — perfect for deployed IoT devices!

---

## Table of Contents

1. [What is SinricPro OTA?](#-what-is-sinricpro-ota)
2. [Prerequisites](#-prerequisites)
3. [Step-by-Step Setup](#-step-by-step-setup)
4. [Understanding the Code](#-understanding-the-code)
5. [Preparing Firmware for OTA](#-preparing-firmware-for-ota)
6. [Triggering OTA Updates](#-triggering-ota-updates)
7. [Debugging OTA Issues](#-debugging-ota-issues)
8. [Best Practices](#-best-practices)
9. [FAQ](#-faq)

---

## What is SinricPro OTA?

SinricPro OTA (Over-The-Air) is a feature that allows your ESP device to **download and install new firmware** from the server when triggered via the SinricPro dashboard.

- Version checking (SemVer support)
- Forced updates
- Progress and error reporting

---

## Prerequisites

Before you begin, ensure you have:

- An ESP32 or ESP8266 board
- Arduino IDE (or PlatformIO) installed
- Latest [SinricPro Library](https://github.com/sinricpro/esp8266-esp32-sdk) installed via Library Manager
- WiFi network credentials
- SinricPro account with:
  - `APP_KEY` and `APP_SECRET` from [portal.sinric.pro](https://portal.sinric.pro)
  - Device ID (`SWITCH_ID`, etc.) for your device


💡 *Complete code: [here](https://github.com/sinricpro/esp8266-esp32-sdk/tree/master/examples/OTAUpdate)*

---

## Step-by-Step Setup

### 1. Install Required Libraries

In Arduino IDE:

- Go to **Sketch → Include Library → Manage Libraries**
- Search and install:
  - `SinricPro`
---

### 2. Configure Your Sketch

Copy and paste the example code below into your Arduino IDE.

> **IMPORTANT**: `FIRMWARE_VERSION` is the Firmware version. You can see in the Portal -> Module. It must be define above must be above SinricPro.h

```cpp
// Uncomment the following line to enable serial debug output
// #define SINRICPRO_NOSSL // Uncomment if you have memory limitation issues.
// #define ENABLE_DEBUG // Enable SDK logging.

// Your firmware version. Must be above SinricPro.h. Do not rename this.
#define FIRMWARE_VERSION "1.1.1"  

#ifdef ENABLE_DEBUG
  #define DEBUG_ESP_PORT Serial
  #define NODEBUG_WEBSOCKETS
  #define NDEBUG
#endif

#include <Arduino.h>

#if defined(ESP8266)
  #include <ESP8266WiFi.h>
  #include "ESP8266OTAHelper.h" // Ref: https://github.com/sinricpro/esp8266-esp32-sdk/tree/master/examples/OTAUpdate
#elif defined(ESP32) 
  #include <WiFi.h>
  #include "ESP32OTAHelper.h" // Ref: https://github.com/sinricpro/esp8266-esp32-sdk/tree/master/examples/OTAUpdate
#elif defined(ARDUINO_ARCH_RP2040)  
  #include <WiFi.h>
  #include "ESP8266OTAHelper.h" // Ref: https://github.com/sinricpro/esp8266-esp32-sdk/tree/master/examples/OTAUpdate
#endif

#include "SemVer.h"
#include "SinricPro.h"
#include "SinricProSwitch.h"


#define WIFI_SSID   "YOUR-WIFI-SSID"
#define WIFI_PASS   "YOUR-WIFI-PASSWORD"
#define APP_KEY     "YOUR-APP-KEY"      // Should look like "de0bxxxx-1x3x-4x3x-ax2x-5dabxxxxxxxx"
#define APP_SECRET  "YOUR-APP-SECRET"   // Should look like "5f36xxxx-x3x7-4x3x-xexe-e86724a9xxxx-4c4axxxx-3x3x-x5xe-x9x3-333d65xxxxxx"
#define SWITCH_ID   "SWITCH_ID"         // Should look like "5dc1564130xxxxxxxxxxxxxx"

#define BAUD_RATE   115200             // Change baudrate to your need

bool handleOTAUpdate(const String& url, int major, int minor, int patch, bool forceUpdate) {
  Version currentVersion  = Version(FIRMWARE_VERSION);
  Version newVersion      = Version(String(major) + "." + String(minor) + "." + String(patch));
  bool updateAvailable    = newVersion > currentVersion;

  Serial.print("URL: ");
  Serial.println(url.c_str());
  Serial.print("Current version: ");
  Serial.println(currentVersion.toString());
  Serial.print("New version: ");
  Serial.println(newVersion.toString());
  if (forceUpdate) Serial.println("Enforcing OTA update!");

  // Handle OTA update based on forceUpdate flag and update availability
  if (forceUpdate || updateAvailable) {
    if (updateAvailable) {
      Serial.println("Update available!");
    }

    String result = startOtaUpdate(url);
    if (!result.isEmpty()) {
      SinricPro.setResponseMessage(std::move(result));
      return false;
    } 
    return true;
  } else {
    String result = "Current version is up to date.";
    SinricPro.setResponseMessage(std::move(result));
    Serial.println(result);
    return false;
  }
}

// setup function for WiFi connection
void setupWiFi() {
  Serial.printf("\r\n[Wifi]: Connecting");

#if defined(ESP8266)
  WiFi.setSleepMode(WIFI_NONE_SLEEP);
  WiFi.setAutoReconnect(true);
#elif defined(ESP32)
  WiFi.setSleep(false);
  WiFi.setAutoReconnect(true);
#endif

  WiFi.begin(WIFI_SSID, WIFI_PASS);

  while (WiFi.status() != WL_CONNECTED) {
    Serial.printf(".");
    delay(250);
  }

  Serial.printf("connected!\r\n[WiFi]: IP-Address is %s\r\n", WiFi.localIP().toString().c_str());
}

// setup function for SinricPro
void setupSinricPro() {
   SinricProSwitch& mySwitch = SinricPro[SWITCH_ID];

  // setup SinricPro
  SinricPro.onConnected([]() {
    Serial.printf("Connected to SinricPro\r\n");
  });
  SinricPro.onDisconnected([]() {
    Serial.printf("Disconnected from SinricPro\r\n");
  });
  SinricPro.onOTAUpdate(handleOTAUpdate);
  SinricPro.begin(APP_KEY, APP_SECRET);
}

// main setup function
void setup() {
  Serial.begin(BAUD_RATE);
  Serial.printf("\r\n\r\n");
  setupWiFi();
  setupSinricPro();
}

void loop() {
  SinricPro.handle();
}
```

---

 
### Firmware Version

```cpp
#define FIRMWARE_VERSION "1.1.1"
```

- This **must** be defined before including `SinricPro.h`.
- Follow [Semantic Versioning](https://semver.org/) (e.g., `MAJOR.MINOR.PATCH`).
- This will be reflected in Portal -> Module section once the device connect to server.

---
 

## Preparing Firmware for OTA

### Step 1: Compile and Export Binary

In Arduino IDE:

- Go to **Sketch → Export Compiled Binary**
- This generates a `.bin` file in your sketch folder.

> Example: `your_sketch.ino.esp32.bin`

---
 

## Triggering OTA Updates

1. [Login](http://portal.sinric.pro) to your Sinric Pro account.

2. Go to **OTA Updates** menu on left.

3. Click **Add OTA Update** button.

4. Enter the the new firmware version. Select the OTA .bin file (`your_sketch.ino.esp32.bin`) and the modules you want to apply the OTA update.

5. Click **Save**

<img src="https://help.sinric.pro/public/img/sinricpro-ota-update-upload.png">

Once you click the **Save** button, the server will broadcast an OTA update request to the selected modules. If your module is currently offline, it will receive the update the next time it connects to the server.


<img src="https://help.sinric.pro/public/img/sinricpro-ota-update-inprogress.png">


---

## Debugging OTA Issues

Enable debug output by uncommenting:

```cpp
#define ENABLE_DEBUG
```

Watch Serial Monitor (`115200 baud`) for:

- WiFi connection status
- SinricPro connection
- OTA URL and version comparison
- Download progress and errors

Common issues:

| Symptom | Solution |
|--------|----------|
| `Update failed: connection refused` | Check URL is publicly accessible |
| `Update failed: not enough space` | Reduce sketch size or use `SINRICPRO_NOSSL` |
| `Version not updating` | Ensure `FIRMWARE_VERSION` is updated in sketch before compiling new binary |
| `Device not responding after OTA` | Check for crashes — add delays or watchdog reset |

---

## Best Practices

- Always test OTA on a development device before deploying to production.
- Use Semantic Versioning consistently (`1.0.0`, `1.0.1`, `2.0.0`, etc.).
- Keep firmware size under 1MB for ESP8266 (if using 1MB flash).
- Use HTTPS URLs for secure downloads (SSL enabled by default).
- Add a 5-10 second delay after `SinricPro.handle()` in `loop()` if device is unstable.
- Implement a rollback mechanism or “safe mode” if OTA fails repeatedly.

---

## FAQ

### Q: Can I use OTA with other SinricPro device types (like DimSwitch, Thermostat, etc.)?

✅ **Yes!** The OTA mechanism is device-type agnostic. Just register `SinricPro.onOTAUpdate(...)` regardless of your device class.

---

### Q: What happens if OTA fails?

The device will:
- Keep running the old firmware.
- Report failure via `SinricPro.setResponseMessage(...)`.
- Remain connected to SinricPro (unless crash occurs).

---

### Q: Does OTA work with SSL/HTTPS?

✅ Yes, by default. If you have memory issues on ESP8266, uncomment:

```cpp
#define SINRICPRO_NOSSL
```

> ⚠️ This disables SSL — only use with trusted networks or HTTP URLs.

---

### Q: How long does OTA take?

Depends on:
- Firmware size (typical: 500KB–1MB)
- WiFi speed
- Server speed

Average: **30 seconds to 3 minutes**.

---

You can find the complete example code here: 
[https://github.com/sinricpro/esp8266-esp32-sdk/tree/master/examples/OTAUpdate](https://github.com/sinricpro/esp8266-esp32-sdk/tree/master/examples/OTAUpdate)


