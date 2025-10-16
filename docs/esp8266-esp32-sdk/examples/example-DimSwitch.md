# DimSwitch Example

## Overview
This example demonstrates how to integrate a dimmable switch or light with SinricPro. It showcases the control of basic functionalities such as turning the device on/off and setting or adjusting its dimming level (power level). This example is suitable for developers looking to create smart dimmers or other devices with percentage-based control.

## Key Concepts/Capabilities Demonstrated
*   **Device Type**: `SinricProDimSwitch`
*   **Capabilities Used**:
    *   [`PowerStateController`]: For managing the dimmable switch's on/off state.
    *   [`PowerLevelController`]: For setting and adjusting the dimming level.
*   **SinricPro Integration**: Demonstrates how to initialize SinricPro, register device-specific callbacks, and maintain the connection.
*   **WiFi Connectivity**: Standard ESP-based WiFi connection setup.

## Hardware Requirements
*   An ESP8266 or ESP32 development board.
*   (Implied) A dimmable light or other device that can be controlled by a power level (e.g., via PWM output from the ESP board).

## Setup Instructions
1.  **Install Libraries**: Ensure you have the SinricPro library and its dependencies installed. Refer to the main SDK documentation for installation instructions.
2.  **Update Credentials**: Modify the following `#define` statements in the `DimSwitch.ino` file:
    *   `WIFI_SSID`: Your Wi-Fi network SSID.
    *   `WIFI_PASS`: Your Wi-Fi network password.
    *   `APP_KEY`: Your SinricPro App Key (from the SinricPro portal).
    *   `APP_SECRET`: Your SinricPro App Secret (from the SinricPro portal).
    *   `DIMSWITCH_ID`: The Device ID of your Dimmable Switch, created in the SinricPro portal (ensure it's of type `DIMMABLE_SWITCH`).
3.  **Baud Rate**: Adjust `BAUD_RATE` if needed for your serial monitor.
4.  **Upload Sketch**: Upload the `DimSwitch.ino` sketch to your ESP8266/ESP32 board.
5.  **Monitor Serial**: Open the serial monitor to observe connection status and command logs (uncomment `#define ENABLE_DEBUG` for more verbose output).

## Code Walkthrough

### Global State Struct
```cpp
struct {
  bool powerState = false;
  int powerLevel = 0;
} device_state;
```
This struct holds the current power state (on/off) and dimming level of the dimmable switch. These values are updated by incoming SinricPro commands and should be used to control your physical hardware.

### Callbacks
These functions are invoked when a corresponding command is received from the SinricPro server.

#### `onPowerState(const String &deviceId, bool &state)`
Handles `setPowerState` commands (on/off).
```cpp
bool onPowerState(const String &deviceId, bool &state) {
  Serial.printf("Device %s power turned %s \r\n", deviceId.c_str(), state?"on":"off");
  device_state.powerState = state;
  return true; 
}
```

#### `onPowerLevel(const String &deviceId, int &powerLevel)`
Handles `setPowerLevel` commands, used for setting the absolute dimming level.
```cpp
bool onPowerLevel(const String &deviceId, int &powerLevel) {
  device_state.powerLevel = powerLevel;
  Serial.printf("Device %s power level changed to %d\r\n", deviceId.c_str(), device_state.powerLevel);
  return true;
}
```

#### `onAdjustPowerLevel(const String &deviceId, int &levelDelta)`
Handles `adjustPowerLevel` commands for dimming, calculating the new absolute power level.
```cpp
bool onAdjustPowerLevel(const String &deviceId, int &levelDelta) {
  device_state.powerLevel += levelDelta;
  Serial.printf("Device %s power level changed about %i to %d\r\n", deviceId.c_str(), levelDelta, device_state.powerLevel);
  levelDelta = device_state.powerLevel; // calculate and return absolute power level
  return true;
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
Initializes the SinricPro library and registers all the necessary callbacks for the Dimmable Switch device.
```cpp
void setupSinricPro() {
  SinricProDimSwitch &myDimSwitch = SinricPro[DIMSWITCH_ID];
  myDimSwitch.onPowerState(onPowerState);
  myDimSwitch.onPowerLevel(onPowerLevel);
  myDimSwitch.onAdjustPowerLevel(onAdjustPowerLevel);

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
*   `SinricProDimSwitch.h`

## Tags
`SinricPro`, `Example`, `DimSwitch`, `Dimmable Switch`, `Light`, `PowerState`, `PowerLevel`, `Smart Home`, `ESP8266`, `ESP32`
