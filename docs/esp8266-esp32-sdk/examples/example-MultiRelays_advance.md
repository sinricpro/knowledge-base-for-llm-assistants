# MultiRelays_advance Example

## Overview
This example demonstrates an advanced and elegant method for controlling multiple relays (acting as smart switches) using a single ESP board and integrating them with SinricPro. It utilizes a structured approach with a `std::vector` of `RelayInfo` structs to manage an arbitrary number of relays, making the setup highly scalable, easy to configure, and efficient for controlling numerous devices from a single microcontroller.

## Key Concepts/Capabilities Demonstrated
*   **Device Type**: `SinricProSwitch` (one instance per relay).
*   **Capabilities Used**:
    *   [`PowerStateController`]: For managing the on/off state of each individual relay.
*   **Multi-Device Control**: Controlling numerous smart devices from a single ESP sketch.
*   **Structured Configuration**: Using `std::vector` and `struct` to define and manage device configurations (Device ID, name, GPIO pin).
*   **Dynamic Callback Registration**: Attaching a single, generic callback function to multiple device instances.
*   **Digital Output Control**: Toggling GPIO pins to control physical relays.
*   **SinricPro Integration**: Demonstrates how to initialize SinricPro and maintain the connection for multiple devices.
*   **WiFi Connectivity**: Standard ESP-based WiFi connection setup.

## Hardware Requirements
*   An ESP8266 or ESP32 development board.
*   Multiple relays (e.g., 1-channel, 2-channel, 4-channel, 8-channel relay modules) connected to the ESP board's GPIO pins.
*   Devices to be controlled by the relays (e.g., lights, appliances).

## Setup Instructions
1.  **Install Libraries**: Ensure you have the SinricPro library and its dependencies installed. Refer to the main SDK documentation for installation instructions.
2.  **Define Relay Pins**: The example provides `#define` statements for `RELAYPIN_1` to `RELAYPIN_8` for both ESP8266 and ESP32. Adjust these to the actual GPIO pins connected to your relays. Ensure these pins are suitable for your board and do not conflict with boot-up requirements.
3.  **Configure `relays` vector**: In the `MultiRelays_advance.ino` file, modify the `std::vector<RelayInfo> relays` definition:
    *   For each relay you want to control, add an entry in the format `{"YOUR_DEVICE_ID", "Relay Name", RELAYPIN_X}`.
    *   `"YOUR_DEVICE_ID"`: Replace with the actual Device ID of a `SWITCH` device you created in the SinricPro portal.
    *   `"Relay Name"`: A descriptive name for this specific relay (will appear in serial output).
    *   `RELAYPIN_X`: Use one of the `RELAYPIN_X` defines from step 2.
    *   **Crucial**: Ensure each `deviceId` in this vector exactly matches a `SWITCH` device ID in your SinricPro portal.
4.  **Update Credentials**: Modify the following `#define` statements:
    *   `WIFI_SSID`: Your Wi-Fi network SSID.
    *   `WIFI_PASS`: Your Wi-Fi network password.
    *   `APP_KEY`: Your SinricPro App Key (from the SinricPro portal).
    *   `APP_SECRET`: Your SinricPro App Secret (from the SinricPro portal).
5.  **Baud Rate**: Adjust `BAUD_RATE` if needed for your serial monitor.
6.  **Wiring**: Connect your relays to the defined GPIO pins. Ensure proper power supply for the relays and the controlled devices.
7.  **Upload Sketch**: Upload the `MultiRelays_advance.ino` sketch to your ESP8266/ESP32 board.
8.  **Monitor Serial**: Open the serial monitor to observe connection status and command logs (uncomment `#define ENABLE_DEBUG` for more verbose output).

## Code Walkthrough

### Defines and Relay Pin Assignments
```cpp
#if defined(ESP8266)
  #define RELAYPIN_1 D1
  // ... other ESP8266 pins
#elif defined(ESP32) || defined(ARDUINO_ARCH_RP2040)
  #define RELAYPIN_1 16
  // ... other ESP32 pins
#endif

#define WIFI_SSID  "YOUR_WIFI_SSID"
#define WIFI_PASS  "YOUR_WIFI_PASS"
#define APP_KEY    "YOUR-APP-KEY"    
#define APP_SECRET "YOUR-APP-SECRET" 
#define BAUD_RATE  115200              
```
These define platform-specific GPIO pins for relays and your Wi-Fi and SinricPro credentials.

### Relay Configuration Structure
```cpp
struct RelayInfo {
  String deviceId;
  String name;
  int pin;
};

std::vector<RelayInfo> relays = {
    {"5fxxxxxxxxxxxxxxxxxxxxxx", "Relay 1", RELAYPIN_1},
    {"5fxxxxxxxxxxxxxxxxxxxxxx", "Relay 2", RELAYPIN_2},
    // ... add more relays here
};
```
The `RelayInfo` struct defines the properties for each relay. The `relays` vector is where you list all your individual relay devices, associating their SinricPro Device ID with a friendly name and the GPIO pin they are connected to.

### Callbacks
This single callback function handles `setPowerState` requests for *all* configured relays.

#### `onPowerState(const String &deviceId, bool &state)`
```cpp
bool onPowerState(const String &deviceId, bool &state) {
  for (auto &relay : relays) {                                                            
    if (deviceId == relay.deviceId) {                                                       
      Serial.printf("Device %s turned %s\r\n", relay.name.c_str(), state ? "on" : "off");     
      digitalWrite(relay.pin, state);                                                         
      return true;                                                                            
    }
  }
  return false; 
}
```
This function iterates through the `relays` vector to find the matching `deviceId`. Once found, it prints the relay's name and the new state to the serial monitor, and then uses `digitalWrite()` to control the physical relay connected to `relay.pin`.

### `setupRelayPins()`
Initializes all the GPIO pins defined in the `relays` vector as `OUTPUT` pins.
```cpp
void setupRelayPins() {
  for (auto &relay : relays) {    
    pinMode(relay.pin, OUTPUT);     
  }
}
```

### `setupWiFi()`
Connects the ESP board to your specified Wi-Fi network.
```cpp
void setupWiFi() {
  Serial.printf("\r\n[Wifi]: Connecting");
  WiFi.begin(WIFI_SSID, WIFI_PASS);
  #if defined(ESP8266)
    WiFi.setSleepMode(WIFI_NONE_SLEEP); 
  #elif defined(ESP32)
    WiFi.setSleep(false); 
  #endif
  while (WiFi.status() != WL_CONNECTED) {
    Serial.printf(".");
    delay(250);
  }
  Serial.printf("connected!\r\n[WiFi]: IP-Address is %s\r\n", WiFi.localIP().toString().c_str());
}
```

### `setupSinricPro()`
Initializes the SinricPro library. It dynamically creates `SinricProSwitch` instances for each relay defined in the `relays` vector and attaches the `onPowerState` callback to each of them.
```cpp
void setupSinricPro() {
  for (auto &relay : relays) {                             
    SinricProSwitch &mySwitch = SinricPro[relay.deviceId];   
    mySwitch.onPowerState(onPowerState);                     
  }

  SinricPro.onConnected([]() { Serial.printf("Connected to SinricPro\r\n"); });
  SinricPro.onDisconnected([]() { Serial.printf("Disconnected from SinricPro\r\n"); });

  SinricPro.begin(APP_KEY, APP_SECRET);
}
```

### `setup()` and `loop()`
Standard Arduino functions for initialization and continuous execution.
```cpp
void setup() {
  Serial.begin(BAUD_RATE);
  setupRelayPins();
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
*   `<vector>`

## Tags
`SinricPro`, `Example`, `Multi-Relay`, `Switch`, `Smart Home`, `Scalable`, `ESP8266`, `ESP32`
