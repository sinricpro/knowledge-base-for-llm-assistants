# MultiSwitch_advance Example

## Overview
This advanced example demonstrates how to control multiple SinricPro Switch devices (relays) using a single ESP module, while simultaneously providing local manual control via physical buttons (tactile or toggle switches). It employs a structured and scalable approach using `std::map` to manage device configurations, making it highly flexible for controlling numerous smart home devices. When a physical button is pressed, the corresponding relay state is toggled, and this change is automatically reported back to the SinricPro server, ensuring state synchronization.

## Key Concepts/Capabilities Demonstrated
*   **Device Type**: `SinricProSwitch` (multiple instances).
*   **Capabilities Used**:
    *   [`PowerStateController`]: For managing the on/off state of each individual switch.
*   **Multi-Device Control**: Efficiently managing and controlling numerous smart devices from a single ESP sketch.
*   **Structured Configuration**: Using `std::map` and custom `struct`s (`deviceConfig_t`, `flipSwitchConfig_t`) to define and manage device configurations (Device ID, relay pin, switch pin).
*   **Dynamic Callback Registration**: Attaching a single, generic `onPowerState` callback function to multiple `SinricProSwitch` instances.
*   **Local Manual Control**: Implementing physical buttons (tactile or toggle) for direct control of relays.
*   **Digital I/O**: Controlling digital output pins for relays and reading digital input pins for switches.
*   **Button Debouncing**: Implementing software debouncing for reliable physical switch input.
*   **State Synchronization**: Ensuring that the state of the physical relay (controlled locally) is reflected accurately on the SinricPro server.
*   **SinricPro Integration**: Demonstrates how to initialize SinricPro and maintain the connection for multiple devices.
*   **WiFi Connectivity**: Standard ESP-based WiFi connection setup.

## Hardware Requirements
*   An ESP8266 or ESP32 development board.
*   Multiple relays (e.g., 1-channel, 2-channel, 4-channel relay modules) connected to the ESP board's GPIO pins.
*   Multiple physical buttons (tactile pushbuttons or toggle switches).
*   Devices to be controlled by the relays (e.g., lights, appliances).

## Setup Instructions
1.  **Install Libraries**: Ensure you have the SinricPro library and its dependencies installed. Refer to the main SDK documentation for installation instructions.
2.  **Define Pins**: The example provides `#define` statements for `RELAYPIN_X` and `SWITCHPIN_X` for both ESP8266 and ESP32. Adjust these to the actual GPIO pins connected to your relays and switches. Ensure these pins are suitable for your board and do not conflict with boot-up requirements.
3.  **Tactile Button vs. Toggle Switch**: In the `MultiSwitch_advance.ino` file:
    *   If you are using **tactile buttons** (momentary switches that return to their original state after being pressed), keep `#define TACTILE_BUTTON 1` uncommented.
    *   If you are using **toggle switches** (latching switches that stay in their position), comment out `#define TACTILE_BUTTON 1`.
4.  **Configure `devices` map**: In the `MultiSwitch_advance.ino` file, modify the `std::map<String, deviceConfig_t> devices` definition. For each switch you want to control, add an entry in the format `{"YOUR_DEVICE_ID", { RELAYPIN_X, SWITCHPIN_X }}
    *   `"YOUR_DEVICE_ID"`: Replace with the actual Device ID of a `SWITCH` device you created in the SinricPro portal.
    *   `RELAYPIN_X`: The `RELAYPIN_X` define corresponding to the GPIO pin for the relay.
    *   `SWITCHPIN_X`: The `SWITCHPIN_X` define corresponding to the GPIO pin for the physical switch.
    *   **Crucial**: Ensure the `deviceId` in this map exactly matches a `SWITCH` device ID in your SinricPro portal.
5.  **Update Credentials**: Modify the following `#define` statements:
    *   `WIFI_SSID`: Your Wi-Fi network SSID.
    *   `WIFI_PASS`: Your Wi-Fi network password.
    *   `APP_KEY`: Your SinricPro App Key.
    *   `APP_SECRET`: Your SinricPro App Secret.
6.  **Baud Rate**: Adjust `BAUD_RATE` if needed.
7.  **Wiring**: Connect your relays and physical switches to the defined GPIO pins. Ensure proper power supply for all components. For tactile buttons, connect one side to the `SWITCHPIN_X` and the other to GND (using `INPUT_PULLUP`). For toggle switches, connect one side to `SWITCHPIN_X` and the other to VCC/GND depending on your logic.
8.  **Upload Sketch**: Upload the `MultiSwitch_advance.ino` sketch to your ESP8266/ESP32 board.
9.  **Monitor Serial**: Open the serial monitor to observe connection status and command logs (uncomment `#define ENABLE_DEBUG` for more verbose output).

## Code Walkthrough

### Defines and Pin Assignments
```cpp
#if defined(ESP8266)
  #define RELAYPIN_1 D1
  #define RELAYPIN_2 D2
  // ... other ESP8266 pins
  #define SWITCHPIN_1 D8
  #define SWITCHPIN_2 D7
  // ... other ESP8266 pins
#elif defined(ESP32) || defined(ARDUINO_ARCH_RP2040)
  #define RELAYPIN_1 16
  #define RELAYPIN_2 17
  // ... other ESP32 pins
  #define SWITCHPIN_1 25
  #define SWITCHPIN_2 26
  // ... other ESP32 pins
#endif

#define TACTILE_BUTTON 1 // comment out if using toggle switches
#define DEBOUNCE_TIME 250

#define WIFI_SSID  "YOUR_WIFI_SSID"
#define WIFI_PASS  "YOUR_WIFI_PASS"
#define APP_KEY    "YOUR-APP-KEY"    
#define APP_SECRET "YOUR-APP-SECRET" 
#define BAUD_RATE  115200              
```
These define platform-specific GPIO pins for relays and switches, a flag for button type, debounce time, and your Wi-Fi and SinricPro credentials.

### Relay and Switch Configuration Structures
```cpp
typedef struct {
  int relayPIN;
  int flipSwitchPIN;
} deviceConfig_t;

std::map<String, deviceConfig_t> devices = {
    {"SWITCH_ID_NO_1_HERE", {  RELAYPIN_1, SWITCHPIN_1 }},
    {"SWITCH_ID_NO_2_HERE", {  RELAYPIN_2, SWITCHPIN_2 }},
    // ... add more devices here
};

typedef struct {
  String deviceId;
  bool lastFlipSwitchState;
  unsigned long lastFlipSwitchChange;
} flipSwitchConfig_t;

std::map<int, flipSwitchConfig_t> flipSwitches;    
```
`deviceConfig_t` defines the properties for each relay/switch pair. The `devices` map is the central configuration for all your switches. `flipSwitchConfig_t` and `flipSwitches` map are used internally to manage the state and debounce time for each physical switch.

### `setupRelays()`
Initializes all the GPIO pins defined in the `devices` map as `OUTPUT` pins for the relays.
```cpp
void setupRelays() { 
  for (auto &device : devices) {           
    int relayPIN = device.second.relayPIN; 
    pinMode(relayPIN, OUTPUT);             
  }
}
```

### `setupFlipSwitches()`
Initializes all the GPIO pins defined in the `devices` map as `INPUT` pins for the physical switches and populates the `flipSwitches` map with initial states and debounce timers.
```cpp
void setupFlipSwitches() {
  for (auto &device : devices)  {                     
    flipSwitchConfig_t flipSwitchConfig;
    flipSwitchConfig.deviceId = device.first;         
    flipSwitchConfig.lastFlipSwitchChange = 0;        
    flipSwitchConfig.lastFlipSwitchState = false;     
    int flipSwitchPIN = device.second.flipSwitchPIN;  
    flipSwitches[flipSwitchPIN] = flipSwitchConfig;   
    pinMode(flipSwitchPIN, INPUT);                   
  }
}
```

### Callbacks
This single callback function handles `setPowerState` requests from SinricPro for *all* configured switches.

#### `onPowerState(String deviceId, bool &state)`
```cpp
bool onPowerState(String deviceId, bool &state)
{
  Serial.printf("%s: %s\r\n", deviceId.c_str(), state ? "on" : "off");
  int relayPIN = devices[deviceId].relayPIN; 
  digitalWrite(relayPIN, state);             
  return true;
}
```
This function iterates through the `devices` map to find the matching `deviceId`. Once found, it prints the relay's name and the new state to the serial monitor, and then uses `digitalWrite()` to control the physical relay connected to `relay.pin`.

### `handleFlipSwitches()`
This function continuously monitors all physical switches. It implements debouncing and, when a stable state change is detected, it toggles the corresponding relay and sends a `sendPowerStateEvent` to SinricPro to synchronize the state.
```cpp
void handleFlipSwitches() {
  unsigned long actualMillis = millis();                                          
  for (auto &flipSwitch : flipSwitches) {                                         
    unsigned long lastFlipSwitchChange = flipSwitch.second.lastFlipSwitchChange;  

    if (actualMillis - lastFlipSwitchChange > DEBOUNCE_TIME) {                    
      int flipSwitchPIN = flipSwitch.first;                                       
      bool lastFlipSwitchState = flipSwitch.second.lastFlipSwitchState;           
      bool flipSwitchState = digitalRead(flipSwitchPIN);                          
      if (flipSwitchState != lastFlipSwitchState) {                               
#ifdef TACTILE_BUTTON
        if (flipSwitchState) {                                                    
#endif      
          flipSwitch.second.lastFlipSwitchChange = actualMillis;                  
          String deviceId = flipSwitch.second.deviceId;                           
          int relayPIN = devices[deviceId].relayPIN;                              
          bool newRelayState = !digitalRead(relayPIN);                            
          digitalWrite(relayPIN, newRelayState);                                  

          SinricProSwitch &mySwitch = SinricPro[deviceId];                        
          bool success = mySwitch.sendPowerStateEvent(newRelayState);             
          if(!success) {
            Serial.printf("Something went wrong...could not send Event to server!\r\n");
          }

#ifdef TACTILE_BUTTON
        }
#endif      
        flipSwitch.second.lastFlipSwitchState = flipSwitchState;                  
      }
    }
  }
}
```

### `setupWiFi()`
Connects the ESP board to your specified Wi-Fi network.
```cpp
void setupWiFi()
{
  Serial.printf("\r\n[Wifi]: Connecting");
  WiFi.begin(WIFI_SSID, WIFI_PASS);
  #if defined(ESP8266)
    WiFi.setSleepMode(WIFI_NONE_SLEEP); 
  #elif defined(ESP32)
    WiFi.setSleep(false); 
  #endif
  while (WiFi.status() != WL_CONNECTED)
  {
    Serial.printf(".");
    delay(250);
  }
  digitalWrite(LED_BUILTIN, HIGH);
  Serial.printf("connected!\r\n[WiFi]: IP-Address is %s\r\n", WiFi.localIP().toString().c_str());
}
```

### `setupSinricPro()`
Initializes the SinricPro library. It dynamically creates `SinricProSwitch` instances for each relay defined in the `devices` map and attaches the `onPowerState` callback to each of them.
```cpp
void setupSinricPro()
{
  for (auto &device : devices)
  {
    const char *deviceId = device.first.c_str();
    SinricProSwitch &mySwitch = SinricPro[deviceId];
    mySwitch.onPowerState(onPowerState);
  }

  SinricPro.begin(APP_KEY, APP_SECRET);  
}
```

### `setup()` and `loop()`
Standard Arduino functions for initialization and continuous execution.
```cpp
void setup()
{
  Serial.begin(BAUD_RATE);
  setupRelays();
  setupFlipSwitches();
  setupWiFi();
  setupSinricPro();
}

void loop()
{
  SinricPro.handle();
  handleFlipSwitches();
}
```

The `loop()` function continuously calls `SinricPro.handle()` to process SinricPro events and `handleFlipSwitches()` to monitor physical switches and synchronize their states.

## Dependencies
*   `Arduino.h`
*   `ESP8266WiFi.h` (for ESP8266) or `WiFi.h` (for ESP32/RP2040)
*   `SinricPro.h`
*   `SinricProSwitch.h`
*   `<map>`

## Tags
`SinricPro`, `Example`, `Multi-Switch`, `Relay`, `Tactile Button`, `Toggle Switch`, `Local Control`, `State Synchronization`, `Smart Home`, `Advanced`, `ESP8266`, `ESP32`
