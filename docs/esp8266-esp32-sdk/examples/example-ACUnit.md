# ACUnit Example

## Overview
This example demonstrates how to integrate a smart AC Unit (specifically a Window AC) with SinricPro. It showcases the control of basic functionalities such as turning the AC on/off, setting and adjusting the target temperature, changing the thermostat mode (e.g., AUTO, COOL, HEAT), and controlling the fan speed using a range value. This example is suitable for developers looking to create smart AC units or similar climate control devices.

## Key Concepts/Capabilities Demonstrated
*   **Device Type**: `SinricProWindowAC`
*   **Capabilities Used**:
    *   [`PowerStateController`]: For managing the AC unit's on/off state.
    *   [`ThermostatController`]: For setting and adjusting target temperatures, and controlling thermostat modes.
    *   [`RangeController`]: Used here to control the fan speed of the AC unit.
*   **SinricPro Integration**: Demonstrates how to initialize SinricPro, register device-specific callbacks, and maintain the connection.
*   **WiFi Connectivity**: Standard ESP-based WiFi connection setup.

## Hardware Requirements
*   An ESP8266 or ESP32 development board.
*   (Implied) An AC unit or a system that can simulate AC unit control (e.g., via relays, IR blasters, or serial commands).

## Setup Instructions
1.  **Install Libraries**: Ensure you have the SinricPro library and its dependencies installed in your Arduino IDE or PlatformIO setup. Refer to the main SDK documentation for installation instructions.
2.  **Update Credentials**: Modify the following `#define` statements in the `ACUnit.ino` file:
    *   `WIFI_SSID`: Your Wi-Fi network SSID.
    *   `WIFI_PASS`: Your Wi-Fi network password.
    *   `APP_KEY`: Your SinricPro App Key (from the SinricPro portal).
    *   `APP_SECRET`: Your SinricPro App Secret (from the SinricPro portal).
    *   `ACUNIT_ID`: The Device ID of your AC Unit, created in the SinricPro portal (ensure it's of type `AC_UNIT`).
3.  **Baud Rate**: Adjust `BAUD_RATE` if needed for your serial monitor.
4.  **Upload Sketch**: Upload the `ACUnit.ino` sketch to your ESP8266/ESP32 board.
5.  **Monitor Serial**: Open the serial monitor to observe connection status, incoming commands, and debug messages (uncomment `#define ENABLE_DEBUG` for more verbose output).

## Code Walkthrough

### Global Variables
```cpp
float globalTemperature;
bool globalPowerState;
int globalFanSpeed;
```
These variables store the current state of the AC unit, which is updated by incoming SinricPro commands and can be used to reflect the state of your physical AC unit.

### Callbacks
These functions are invoked when a corresponding command is received from the SinricPro server.

#### `onPowerState(const String &deviceId, bool &state)`
Handles `setPowerState` commands (on/off).
```cpp
bool onPowerState(const String &deviceId, bool &state) {
  Serial.printf("Thermostat %s turned %s\r\n", deviceId.c_str(), state?"on":"off");
  globalPowerState = state; 
  return true; // request handled properly
}
```

#### `onTargetTemperature(const String &deviceId, float &temperature)`
Handles `setTargetTemperature` commands.
```cpp
bool onTargetTemperature(const String &deviceId, float &temperature) {
  Serial.printf("Thermostat %s set temperature to %f\r\n", deviceId.c_str(), temperature);
  globalTemperature = temperature;
  return true;
}
```

#### `onAdjustTargetTemperature(const String &deviceId, float &temperatureDelta)`
Handles `adjustTargetTemperature` commands, calculating the new absolute temperature.
```cpp
bool onAdjustTargetTemperature(const String & deviceId, float &temperatureDelta) {
  globalTemperature += temperatureDelta;  // calculate absolut temperature
  Serial.printf("Thermostat %s changed temperature about %f to %f", deviceId.c_str(), temperatureDelta, globalTemperature);
  temperatureDelta = globalTemperature; // return absolut temperature
  return true;
}
```

#### `onThermostatMode(const String &deviceId, String &mode)`
Handles `setThermostatMode` commands.
```cpp
bool onThermostatMode(const String &deviceId, String &mode) {
  Serial.printf("Thermostat %s set to mode %s\r\n", deviceId.c_str(), mode.c_str());
  return true;
}
```

#### `onRangeValue(const String &deviceId, int &rangeValue)`
Handles `setRangeValue` commands, used for fan speed.
```cpp
bool onRangeValue(const String &deviceId, int &rangeValue) {
  Serial.printf("Fan speed set to %d\r\n", rangeValue);
  globalFanSpeed = rangeValue;
  return true;
}
```

#### `onAdjustRangeValue(const String &deviceId, int &valueDelta)`
Handles `adjustRangeValue` commands for fan speed, calculating the new absolute speed.
```cpp
bool onAdjustRangeValue(const String &deviceId, int &valueDelta) {
  globalFanSpeed += valueDelta;
  Serial.printf("Fan speed changed about %d to %d\r\n", valueDelta, globalFanSpeed);
  valueDelta = globalFanSpeed;
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
Initializes the SinricPro library and registers all the necessary callbacks for the AC unit device.
```cpp
void setupSinricPro() {
  SinricProWindowAC &myAcUnit = SinricPro[ACUNIT_ID];
  myAcUnit.onPowerState(onPowerState);
  myAcUnit.onTargetTemperature(onTargetTemperature);
  myAcUnit.onAdjustTargetTemperature(onAdjustTargetTemperature);
  myAcUnit.onThermostatMode(onThermostatMode);
  myAcUnit.onRangeValue(onRangeValue);
  myAcUnit.onAdjustRangeValue(onAdjustRangeValue);

  // setup SinricPro
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
*   `SinricProWindowAC.h`

## Tags
`SinricPro`, `Example`, `AC Unit`, `Window AC`, `Thermostat`, `PowerState`, `Temperature`, `Range`, `Fan Speed`, `Smart Home`, `ESP8266`, `ESP32`
