# Thermostat Example

## Overview
This example demonstrates how to integrate a smart thermostat device with SinricPro. It showcases the control of basic functionalities such as turning the thermostat on/off, setting and adjusting the target temperature, and changing the thermostat mode (e.g., AUTO, COOL, HEAT). This example is suitable for developers looking to create smart climate control devices.

## Key Concepts/Capabilities Demonstrated
*   **Device Type**: `SinricProThermostat`
*   **Capabilities Used**: 
    *   [`PowerStateController`]: For managing the thermostat's on/off state.
    *   [`ThermostatController`]: For setting and adjusting target temperatures, and controlling thermostat modes.
*   **SinricPro Integration**: Demonstrates how to initialize SinricPro, register device-specific callbacks, and maintain the connection.
*   **WiFi Connectivity**: Standard ESP-based WiFi connection setup.

## Hardware Requirements
*   An ESP8266 or ESP32 development board.
*   (Implied) A heating/cooling system that can be controlled by the microcontroller (e.g., via relays or other control signals).

## Setup Instructions
1.  **Install Libraries**: Ensure you have the SinricPro library and its dependencies installed. Refer to the main SDK documentation for installation instructions.
2.  **Update Credentials**: Modify the following `#define` statements in the `Thermostat.ino` file:
    *   `WIFI_SSID`: Your Wi-Fi network SSID.
    *   `WIFI_PASS`: Your Wi-Fi network password.
    *   `APP_KEY`: Your SinricPro App Key (from the SinricPro portal).
    *   `APP_SECRET`: Your SinricPro App Secret (from the SinricPro portal).
    *   `THERMOSTAT_ID`: The Device ID of your Thermostat, created in the SinricPro portal (ensure it's of type `THERMOSTAT`).
3.  **Baud Rate**: Adjust `BAUD_RATE` if needed for your serial monitor.
4.  **Upload Sketch**: Upload the `Thermostat.ino` sketch to your ESP8266/ESP32 board.
5.  **Monitor Serial**: Open the serial monitor to observe connection status and command logs (uncomment `#define ENABLE_DEBUG` for more verbose output).

## Code Walkthrough

### Defines and Global State
```cpp
#define WIFI_SSID         "YOUR_WIFI_SSID"    
#define WIFI_PASS         "YOUR_WIFI_PASSWORD"
#define APP_KEY           "YOUR_APP_KEY_HERE"      
#define APP_SECRET        "YOUR_APP_SECRET_HERE"   
#define THERMOSTAT_ID     "YOUR_DEVICE_ID_HERE"    
#define BAUD_RATE         115200                     

float globalTemperature;
bool globalPowerState;
```
These define your Wi-Fi and SinricPro credentials, the thermostat device ID, serial baud rate. `globalTemperature` and `globalPowerState` store the current state of the thermostat.

### Callbacks
These functions are invoked when a corresponding command is received from the SinricPro server.

#### `onPowerState(const String &deviceId, bool &state)`
Handles `setPowerState` commands (on/off). Updates `globalPowerState`.
```cpp
bool onPowerState(const String &deviceId, bool &state) {
  Serial.printf("Thermostat %s turned %s\r\n", deviceId.c_str(), state?"on":"off");
  globalPowerState = state; 
  return true; 
}
```

#### `onTargetTemperature(const String &deviceId, float &temperature)`
Handles `setTargetTemperature` commands. Updates `globalTemperature`.
```cpp
bool onTargetTemperature(const String &deviceId, float &temperature) {
  Serial.printf("Thermostat %s set temperature to %f\r\n", deviceId.c_str(), temperature);
  globalTemperature = temperature;
  return true;
}
```

#### `onAdjustTargetTemperature(const String &deviceId, float &temperatureDelta)`
Handles `adjustTargetTemperature` commands. Calculates the new absolute temperature, prints, and updates `globalTemperature`. `temperatureDelta` is updated to return the absolute temperature.
```cpp
bool onAdjustTargetTemperature(const String & deviceId, float &temperatureDelta) {
  globalTemperature += temperatureDelta;  
  Serial.printf("Thermostat %s changed temperature about %f to %f", deviceId.c_str(), temperatureDelta, globalTemperature);
  temperatureDelta = globalTemperature; 
  return true;
}
```

#### `onThermostatMode(const String &deviceId, String &mode)`
Handles `setThermostatMode` commands. Logs the received mode.
```cpp
bool onThermostatMode(const String &deviceId, String &mode) {
  Serial.printf("Thermostat %s set to mode %s\r\n", deviceId.c_str(), mode.c_str());
  // Implement your physical thermostat mode control here
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
Initializes the SinricPro library and registers all the necessary callbacks for the Thermostat device.
```cpp
void setupSinricPro() {
  SinricProThermostat &myThermostat = SinricPro[THERMOSTAT_ID];
  myThermostat.onPowerState(onPowerState);
  myThermostat.onTargetTemperature(onTargetTemperature);
  myThermostat.onAdjustTargetTemperature(onAdjustTargetTemperature);
  myThermostat.onThermostatMode(onThermostatMode);

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

The `loop()` function continuously calls `SinricPro.handle()` to process SinricPro events and maintain the connection.

## Dependencies
*   `Arduino.h`
*   `ESP8266WiFi.h` (for ESP8266) or `WiFi.h` (for ESP32/RP2040)
*   `SinricPro.h`
*   `SinricProThermostat.h`

## Tags
`SinricPro`, `Example`, `Thermostat`, `Temperature Control`, `Mode Control`, `Smart Home`, `HVAC`, `ESP8266`, `ESP32`
