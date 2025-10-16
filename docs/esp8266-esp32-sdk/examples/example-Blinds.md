# Blinds Example

## Overview
This example demonstrates how to integrate a smart blinds device with SinricPro. It showcases the control of basic functionalities such as turning the blinds on/off, setting them to an absolute open percentage (0-100%), and adjusting their position relatively. This example is suitable for developers looking to create smart blinds or curtain control systems.

## Key Concepts/Capabilities Demonstrated
*   **Device Type**: `SinricProBlinds`
*   **Capabilities Used**: 
    *   [`PowerStateController`]: For managing the blinds' on/off state.
    *   [`RangeController`]: For setting and adjusting the blinds' open percentage.
*   **SinricPro Integration**: Demonstrates how to initialize SinricPro, register device-specific callbacks, and maintain the connection.
*   **WiFi Connectivity**: Standard ESP-based WiFi connection setup.

## Hardware Requirements
*   An ESP8266 or ESP32 development board.
*   (Implied) A motor and mechanism to control physical blinds or curtains.

## Setup Instructions
1.  **Install Libraries**: Ensure you have the SinricPro library and its dependencies installed. Refer to the main SDK documentation for installation instructions.
2.  **Update Credentials**: Modify the following `#define` statements in the `Blinds.ino` file:
    *   `WIFI_SSID`: Your Wi-Fi network SSID.
    *   `WIFI_PASS`: Your Wi-Fi network password.
    *   `APP_KEY`: Your SinricPro App Key (from the SinricPro portal).
    *   `APP_SECRET`: Your SinricPro App Secret (from the SinricPro portal).
    *   `BLINDS_ID`: The Device ID of your Blinds, created in the SinricPro portal (ensure it's of type `BLIND`).
3.  **Baud Rate**: Adjust `BAUD_RATE` if needed for your serial monitor.
4.  **Upload Sketch**: Upload the `Blinds.ino` sketch to your ESP8266/ESP32 board.
5.  **Monitor Serial**: Open the serial monitor to observe connection status and command logs (uncomment `#define ENABLE_DEBUG` for more verbose output).

## Code Walkthrough

### Global Variables
```cpp
int blindsPosition = 0;
bool powerState = false;
```
These variables store the current open percentage and power state of the blinds, which are updated by incoming SinricPro commands and can be used to reflect the state of your physical blinds.

### Callbacks
These functions are invoked when a corresponding command is received from the SinricPro server.

#### `onPowerState(const String &deviceId, bool &state)`
Handles `setPowerState` commands (on/off).
```cpp
bool onPowerState(const String &deviceId, bool &state) {
  Serial.printf("Device %s power turned %s \r\n", deviceId.c_str(), state?"on":"off");
  powerState = state;
  return true; // request handled properly
}
```

#### `onRangeValue(const String &deviceId, int &position)`
Handles `setRangeValue` commands, used for setting the absolute blinds position.
```cpp
bool onRangeValue(const String &deviceId, int &position) {
  Serial.printf("Device %s set position to %d\r\n", deviceId.c_str(), position);
  return true; // request handled properly
}
```

#### `onAdjustRangeValue(const String &deviceId, int &positionDelta)`
Handles `adjustRangeValue` commands for blinds position, calculating the new absolute position.
```cpp
bool onAdjustRangeValue(const String &deviceId, int &positionDelta) {
  blindsPosition += positionDelta;
  Serial.printf("Device %s position changed about %i to %d\r\n", deviceId.c_str(), positionDelta, blindsPosition);
  positionDelta = blindsPosition; // calculate and return absolute position
  return true; // request handled properly
}
```

### `setupWiFi()`
Connects the ESP board to your specified Wi-Fi network.
```cpp
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
  IPAddress localIP = WiFi.localIP();
  Serial.printf("connected!\r\n[WiFi]: IP-Address is %d.%d.%d.%d\r\n", localIP[0], localIP[1], localIP[2], localIP[3]);
}
```

### `setupSinricPro()`
Initializes the SinricPro library and registers all the necessary callbacks for the Blinds device.
```cpp
void setupSinricPro() {
  SinricProBlinds &myBlinds = SinricPro[BLINDS_ID];
  myBlinds.onPowerState(onPowerState);
  myBlinds.onRangeValue(onRangeValue);
  myBlinds.onAdjustRangeValue(onAdjustRangeValue);

  SinricPro.onConnected([](){ Serial.printf("Connected to SinricPro\r\n"); }); 
  SinricPro.onDisconnected([](){ Serial.printf("Disconnected from SinricPro\r\n"); });
  SinricPro.begin(APP_KEY, APP_SECRET);
}
```

### `setup()` and `loop()`
Standard Arduino functions for initialization and continuous execution.
```cpp
void setup() {
  Serial.begin(BAUD_RATE); Serial.printf("\r\n\r\n");
  setupWiFi();
  setupSinricPro();
}

void loop() {
  SinricPro.handle();
}
```

## Dependencies
*   `Arduino.h`
*   `ESP8266WiFi.h` (for ESP8266) or `WiFi.h` (for ESP32/RP2040)
*   `SinricPro.h`
*   `SinricProBlinds.h`

## Tags
`SinricPro`, `Example`, `Blinds`, `Curtains`, `Range`, `PowerState`, `Smart Home`, `ESP8266`, `ESP32`
