# MultiSwitch_beginner Example

## Overview
This example demonstrates a straightforward approach to controlling multiple SinricPro Switch devices (relays) using a single ESP module. Each switch is configured with its own unique Device ID and has a dedicated callback function to handle its `setPowerState` commands. This is a good starting point for beginners to understand multi-device control with SinricPro, especially when dealing with a small, fixed number of devices.

## Key Concepts/Capabilities Demonstrated
*   **Device Type**: `SinricProSwitch` (multiple instances).
*   **Capabilities Used**: 
    *   [`PowerStateController`]: For managing the on/off state of each individual switch.
*   **Multi-Device Control**: Controlling several smart devices from a single sketch.
*   **Dedicated Callbacks**: Assigning a unique callback function for each device's specific actions.
*   **SinricPro Integration**: Demonstrates how to initialize SinricPro and maintain the connection for multiple devices.
*   **WiFi Connectivity**: Standard ESP-based WiFi connection setup.

## Hardware Requirements
*   An ESP8266 or ESP32 development board.
*   Multiple relays (e.g., 1-channel, 2-channel, 4-channel relay modules).
*   Devices to be controlled by the relays.

## Setup Instructions
1.  **Install Libraries**: Ensure you have the SinricPro library and its dependencies installed. Refer to the main SDK documentation for installation instructions.
2.  **Update Device IDs**: Modify the `SWITCH_ID_1` to `SWITCH_ID_4` `#define` statements in the `MultiSwitch_beginner.ino` file with the actual Device IDs of your `SWITCH` devices created in the SinricPro portal.
3.  **Update Credentials**: Modify the following `#define` statements:
    *   `WIFI_SSID`: Your Wi-Fi network SSID.
    *   `WIFI_PASS`: Your Wi-Fi network password.
    *   `APP_KEY`: Your SinricPro App Key.
    *   `APP_SECRET`: Your SinricPro App Secret.
4.  **Baud Rate**: Adjust `BAUD_RATE` if needed for your serial monitor.
5.  **Wiring**: Connect your relays to the GPIO pins you intend to use. Remember to implement the actual `digitalWrite` calls within the `onPowerStateX` functions to control your physical relays.
6.  **Upload Sketch**: Upload the `MultiSwitch_beginner.ino` sketch to your ESP8266/ESP32 board.
7.  **Monitor Serial**: Open the serial monitor to observe connection status and command logs (uncomment `#define ENABLE_DEBUG` for more verbose output).

## Code Walkthrough

### Defines
```cpp
#define WIFI_SSID         "YOUR-WIFI-SSID"    
#define WIFI_PASS         "YOUR-WIFI-PASSWORD" 
#define APP_KEY           "YOUR-APP-KEY"      
#define APP_SECRET        "YOUR-APP-SECRET"   

#define SWITCH_ID_1       "YOUR-DEVICE-ID"    
#define SWITCH_ID_2       "YOUR-DEVICE-ID"    
#define SWITCH_ID_3       "YOUR-DEVICE-ID"    
#define SWITCH_ID_4       "YOUR-DEVICE-ID"    

#define BAUD_RATE         115200                
```
These define your Wi-Fi and SinricPro credentials, and the unique Device IDs for each of your four switches.

### Callbacks
Each of these functions is a dedicated callback for a specific switch. When a `setPowerState` command is received for `SWITCH_ID_1`, `onPowerState1` is called, and so on. In a real application, you would add `digitalWrite()` calls here to control the physical relays connected to each switch.

#### `onPowerState1(const String &deviceId, bool &state)`
```cpp
bool onPowerState1(const String &deviceId, bool &state) {
  Serial.printf("Device 1 turned %s\r\n", state?"on":"off");
  // digitalWrite(RELAY_PIN_1, state); // Example: control physical relay
  return true; 
}
```

#### `onPowerState2(const String &deviceId, bool &state)`
```cpp
bool onPowerState2(const String &deviceId, bool &state) {
  Serial.printf("Device 2 turned %s\r\n", state?"on":"off");
  // digitalWrite(RELAY_PIN_2, state); // Example: control physical relay
  return true; 
}
```

#### `onPowerState3(const String &deviceId, bool &state)`
```cpp
bool onPowerState3(const String &deviceId, bool &state) {
  Serial.printf("Device 3 turned %s\r\n", state?"on":"off");
  // digitalWrite(RELAY_PIN_3, state); // Example: control physical relay
  return true; 
}
```

#### `onPowerState4(const String &deviceId, bool &state)`
```cpp
bool onPowerState4(const String &deviceId, bool &state) {
  Serial.printf("Device 4 turned %s\r\n", state?"on":"off");
  // digitalWrite(RELAY_PIN_4, state); // Example: control physical relay
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
Initializes the SinricPro library and adds each switch device, registering its dedicated `onPowerState` callback.
```cpp
void setupSinricPro() {
  SinricProSwitch& mySwitch1 = SinricPro[SWITCH_ID_1];
  mySwitch1.onPowerState(onPowerState1);

  SinricProSwitch& mySwitch2 = SinricPro[SWITCH_ID_2];
  mySwitch2.onPowerState(onPowerState2);

  SinricProSwitch& mySwitch3 = SinricPro[SWITCH_ID_3];
  mySwitch3.onPowerState(onPowerState3);

  SinricProSwitch& mySwitch4 = SinricPro[SWITCH_ID_4];
  mySwitch4.onPowerState(onPowerState4);

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
*   `SinricProSwitch.h`

## Tags
`SinricPro`, `Example`, `Multi-Switch`, `Switch`, `Relay`, `Basic`, `Smart Home`, `ESP8266`, `ESP32`
