# Fan Example

## Overview
This example demonstrates how to integrate a smart fan device with SinricPro. It showcases the control of basic functionalities such as turning the fan on/off and setting or adjusting its speed using a range value (typically representing discrete speed levels like 1, 2, 3). This example is suitable for developers looking to create smart fan controllers.

## Key Concepts/Capabilities Demonstrated
*   **Device Type**: `SinricProFanUS`
*   **Capabilities Used**:
    *   [`PowerStateController`]: For managing the fan's on/off state.
    *   [`RangeController`]: For setting and adjusting the fan speed.
*   **SinricPro Integration**: Demonstrates how to initialize SinricPro, register device-specific callbacks, and maintain the connection.
*   **WiFi Connectivity**: Standard ESP-based WiFi connection setup.

## Hardware Requirements
*   An ESP8266 or ESP32 development board.
*   (Implied) A fan that can be controlled (e.g., via relays for discrete speeds, or PWM for continuous speed control).

## Setup Instructions
1.  **Install Libraries**: Ensure you have the SinricPro library and its dependencies installed. Refer to the main SDK documentation for installation instructions.
2.  **Update Credentials**: Modify the following `#define` statements in the `Fan.ino` file:
    *   `WIFI_SSID`: Your Wi-Fi network SSID.
    *   `WIFI_PASS`: Your Wi-Fi network password.
    *   `APP_KEY`: Your SinricPro App Key (from the SinricPro portal).
    *   `APP_SECRET`: Your SinricPro App Secret (from the SinricPro portal).
    *   `FAN_ID`: The Device ID of your Fan, created in the SinricPro portal (ensure it's of type `FAN`).
3.  **Baud Rate**: Adjust `BAUD_RATE` if needed for your serial monitor.
4.  **Upload Sketch**: Upload the `Fan.ino` sketch to your ESP8266/ESP32 board.
5.  **Monitor Serial**: Open the serial monitor to observe connection status and command logs (uncomment `#define ENABLE_DEBUG` for more verbose output).

## Code Walkthrough

### Global State Struct
```cpp
struct {
  bool powerState = false;
  int fanSpeed = 1;
} device_state;
```
This struct holds the current power state (on/off) and fan speed of the fan. These values are updated by incoming SinricPro commands and should be used to control your physical fan.

### Callbacks
These functions are invoked when a corresponding command is received from the SinricPro server.

#### `onPowerState(const String &deviceId, bool &state)`
Handles `setPowerState` commands (on/off).
```cpp
bool onPowerState(const String &deviceId, bool &state) {
  Serial.printf("Fan turned %s\r\n", state?"on":"off");
  device_state.powerState = state;
  return true; 
}
```

#### `onRangeValue(const String &deviceId, int &rangeValue)`
Handles `setRangeValue` commands, used for setting the absolute fan speed.
```cpp
bool onRangeValue(const String &deviceId, int &rangeValue) {
  device_state.fanSpeed = rangeValue;
  Serial.printf("Fan speed changed to %d\r\n", device_state.fanSpeed);
  return true;
}
```

#### `onAdjustRangeValue(const String &deviceId, int &rangeValueDelta)`
Handles `adjustRangeValue` commands for fan speed, calculating the new absolute speed.
```cpp
bool onAdjustRangeValue(const String &deviceId, int &rangeValueDelta) {
  device_state.fanSpeed += rangeValueDelta;
  Serial.printf("Fan speed changed about %i to %d\r\n", rangeValueDelta, device_state.fanSpeed);
  rangeValueDelta = device_state.fanSpeed; // return absolute fan speed
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
  Serial.printf("connected!\r\n[WiFi]: IP-Address is %s\r\n", WiFi.localIP().toString().c_str());
}
```

### `setupSinricPro()`
Initializes the SinricPro library and registers all the necessary callbacks for the Fan device.
```cpp
void setupSinricPro() {
  SinricProFanUS &myFan = SinricPro[FAN_ID];
  myFan.onPowerState(onPowerState);
  myFan.onRangeValue(onRangeValue);
  myFan.onAdjustRangeValue(onAdjustRangeValue);

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
*   `SinricProFanUS.h`

## Tags
`SinricPro`, `Example`, `Fan`, `Fan Speed`, `Range`, `PowerState`, `Smart Home`, `ESP8266`, `ESP32`
