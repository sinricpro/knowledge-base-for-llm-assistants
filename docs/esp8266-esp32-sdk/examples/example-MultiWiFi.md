# MultiWiFi Example

## Overview
This example demonstrates how to configure and manage primary and secondary Wi-Fi network settings for a SinricPro device, enhancing its connectivity robustness. It utilizes the `SinricProWiFiSettings` helper class and LittleFS for persistent storage of Wi-Fi credentials, allowing the device to automatically connect to an available network and providing a fallback if the primary network is unavailable. This is crucial for reliable smart home device operation.

## Key Concepts/Capabilities Demonstrated
*   **Wi-Fi Management**: Implementing primary and secondary Wi-Fi network configurations.
*   **Persistent Storage**: Using LittleFS (Little File System) to save and load Wi-Fi credentials across reboots.
*   **Remote Configuration**: Handling `onSetSetting` callback to update Wi-Fi credentials remotely via the SinricPro platform.
*   **`SinricProWiFiSettings`**: Utilization of a dedicated helper class for streamlined Wi-Fi management.
*   **SinricPro Integration**: Demonstrates how to initialize SinricPro and maintain the connection.
*   **WiFi Connectivity**: Standard ESP-based WiFi connection setup with auto-reconnect.

## Hardware Requirements
*   An ESP8266 or ESP32 development board.
*   (No specific external hardware required beyond the ESP board itself).

## Setup Instructions
1.  **Install Libraries**: Ensure you have the SinricPro library and its dependencies installed. You will also need `ArduinoJson` and `LittleFS` (or `ESP8266FS` for ESP8266) libraries. Make sure your IDE is configured to upload the LittleFS filesystem data.
2.  **Update Credentials**: Modify the following `#define` statements in the `MultiWiFi.ino` file:
    *   `APP_KEY`: Your SinricPro App Key (from the SinricPro portal).
    *   `APP_SECRET`: Your SinricPro App Secret (from the SinricPro portal).
    *   `SWITCH_ID`: The Device ID of any device (e.g., a Switch) from SinricPro. This example uses a `SinricProSwitch` as a placeholder, but the Wi-Fi management is generic.
3.  **Initial Wi-Fi Credentials**: Set `primarySSID`, `primaryPassword`, `secondarySSID`, `secondaryPassword` with your Wi-Fi network details. These will be the initial credentials used if no saved configuration is found.
4.  **Baud Rate**: Adjust `BAUD_RATE` if needed for your serial monitor.
5.  **Upload Sketch**: Upload the `MultiWiFi.ino` sketch to your ESP8266/ESP32 board.
6.  **Monitor Serial**: Open the serial monitor to observe connection status and logs.
7.  **Remote Configuration (Optional)**: After initial setup, you can remotely update the primary or secondary Wi-Fi settings via the SinricPro portal. Use the `setSetting` action with `id` as `pro.sinric::set.wifi.primary` or `pro.sinric::set.wifi.secondary` and `value` as a JSON string like `{"ssid": "new_ssid", "password": "new_password", "connectNow": true/false}`.

## Code Walkthrough

### Defines and Wi-Fi Credentials
```cpp
#define APP_KEY ""
#define APP_SECRET ""
#define SWITCH_ID ""
#define BAUD_RATE 115200

#define SET_WIFI_PRIMARY "pro.sinric::set.wifi.primary"
#define SET_WIFI_SECONDARY "pro.sinric::set.wifi.secondary"

const bool formatLittleFSIfFailed = true;

const char* primarySSID = "";
const char* primaryPassword = "";
const char* secondarySSID = "";
const char* secondaryPassword = "";
```
These define your SinricPro credentials, a placeholder device ID, serial baud rate, and the unique identifiers for the Wi-Fi setting commands. Initial Wi-Fi credentials are also defined here.

### `SinricProWiFiSettings` Instance
```cpp
SinricProWiFiSettings spws(LittleFS, primarySSID, primaryPassword, secondarySSID, secondaryPassword, "/wificonfig.dat");
```
An instance of `SinricProWiFiSettings` is created, linking it to LittleFS for storage and providing initial Wi-Fi credentials.

### `onSetModuleSetting(const String& id, const String& value)`
This callback function is registered with `SinricPro.onSetSetting()` and handles incoming requests to update Wi-Fi credentials. It parses the JSON payload to extract the new SSID and password, updates the settings using `spws.updatePrimarySettings()` or `spws.updateSecondarySettings()`, and optionally attempts to connect immediately.
```cpp
bool onSetModuleSetting(const String& id, const String& value) {
  JsonDocument doc;
  DeserializationError error = deserializeJson(doc, value);
  if (error) { /* ... error handling ... */ return false; }
  const char* password = doc["password"].as<const char*>();
  const char* ssid = doc["ssid"].as<const char*>();
  bool connectNow = doc["connectNow"] | false;

  if (id == SET_WIFI_PRIMARY) {
    spws.updatePrimarySettings(ssid, password);
  } else if (id == SET_WIFI_SECONDARY) {
    spws.updateSecondarySettings(ssid, password);
  }
  return connectNow ? connectToWiFi(ssid, password) : true;
}
```

### `setupLittleFS()`
Initializes the LittleFS file system. If mounting fails and `formatLittleFSIfFailed` is true, it attempts to format the filesystem.
```cpp
bool setupLittleFS() {
  // ... (LittleFS initialization and optional formatting logic)
}
```

### `connectToWiFi(const char* ssid, const char* password)`
Helper function to connect the ESP board to a specified Wi-Fi network. It includes a timeout for connection attempts.
```cpp
bool connectToWiFi(const char* ssid, const char* password) {
  // ... (WiFi connection logic)
}
```

### `setupWiFi()`
This function is called during setup to establish the Wi-Fi connection. It first attempts to load saved settings from LittleFS using `spws.begin()`. Then, it tries to connect to the primary Wi-Fi network, and if that fails, it attempts to connect to the secondary network.
```cpp
void setupWiFi() {
  Serial.printf("\r\n[Wifi]: Connecting");
  spws.begin();
  auto& settings = spws.getWiFiSettings();
  bool connected = false;
  if (spws.isValidSetting(settings.primarySSID, settings.primaryPassword)) {
    connected = connectToWiFi(settings.primarySSID, settings.primaryPassword);
  }
  if (!connected && spws.isValidSetting(settings.secondarySSID, settings.secondaryPassword)) {
    connected = connectToWiFi(settings.secondarySSID, settings.secondaryPassword);
  }
  if (connected) {
    Serial.println("Connected to WiFi!");
  } else {
    Serial.println("Failed to connect to WiFi!");
  }
}
```

### `setupSinricPro()`
Initializes the SinricPro library and registers the `onSetModuleSetting` callback to enable remote Wi-Fi configuration. It also sets up standard connection status callbacks.
```cpp
void setupSinricPro() {
  SinricProSwitch& mySwitch = SinricPro[SWITCH_ID];
  SinricPro.onConnected([]() { Serial.printf("Connected to SinricPro\r\n"); });
  SinricPro.onDisconnected([]() { Serial.printf("Disconnected from SinricPro\r\n"); });
  SinricPro.onSetSetting(onSetModuleSetting);
  SinricPro.begin(APP_KEY, APP_SECRET);
}
```

### `setup()` and `loop()`
Standard Arduino functions for initialization and continuous execution.
```cpp
void setup() {
  Serial.begin(BAUD_RATE);
  Serial.printf("\r\n\r\n");
  setupLittleFS();
  setupWiFi();
  setupSinricPro();
}

void loop() {
  SinricPro.handle();
  if (WiFi.status() != WL_CONNECTED) ESP.restart(); // Restart if WiFi disconnects
}
```

The `loop()` function continuously calls `SinricPro.handle()` to process SinricPro events and maintains the Wi-Fi connection, restarting the ESP if the connection is lost.

## Dependencies
*   `Arduino.h`
*   `ESP8266WiFi.h` (for ESP8266) or `WiFi.h` (for ESP32)
*   `LittleFS.h`
*   `ArduinoJson.h`
*   `SinricPro.h`
*   `SinricProSwitch.h`
*   `SinricProWiFiSettings.h` (local file in the example directory)

## Tags
`SinricPro`, `Example`, `WiFi`, `Multi-WiFi`, `Persistent Storage`, `LittleFS`, `Remote Configuration`, `Settings`, `ESP8266`, `ESP32`
