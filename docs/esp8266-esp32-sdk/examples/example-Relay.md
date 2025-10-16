# Relay Example

## Overview
This example demonstrates how to control a single relay (acting as a smart switch) using an ESP board and integrate it with SinricPro. It provides basic on/off control for a connected appliance or light, making it a fundamental building block for smart home automation.

## Key Concepts/Capabilities Demonstrated
*   **Device Type**: `SinricProSwitch`
*   **Capabilities Used**:
    *   [`PowerStateController`]: For managing the relay's on/off state.
*   **Digital Output Control**: Toggling a GPIO pin to control a physical relay.
*   **SinricPro Integration**: Demonstrates how to initialize SinricPro, register device-specific callbacks, and maintain the connection.
*   **WiFi Connectivity**: Standard ESP-based WiFi connection setup.

## Hardware Requirements
*   An ESP8266 or ESP32 development board.
*   A single relay module.
*   An appliance or light to be controlled by the relay.

## Setup Instructions
1.  **Install Libraries**: Ensure you have the SinricPro library and its dependencies installed. Refer to the main SDK documentation for installation instructions.
2.  **Update Credentials**: Modify the following `#define` statements in the `Relay.ino` file:
    *   `WIFI_SSID`: Your Wi-Fi network SSID.
    *   `WIFI_PASS`: Your Wi-Fi network password.
    *   `APP_KEY`: Your SinricPro App Key (from the SinricPro portal).
    *   `APP_SECRET`: Your SinricPro App Secret (from the SinricPro portal).
    *   `SWITCH_ID`: The Device ID of your Switch, created in the SinricPro portal (ensure it's of type `SWITCH`).
3.  **Pin Configuration**: Define `RELAY_PIN` to the GPIO pin where your relay is connected. Ensure this pin is suitable for your board and does not conflict with boot-up requirements.
4.  **Baud Rate**: Adjust `BAUD_RATE` if needed for your serial monitor.
5.  **Wiring**: Connect your relay to the defined GPIO pin. Ensure proper power supply for the relay and the controlled device.
6.  **Upload Sketch**: Upload the `Relay.ino` sketch to your ESP8266/ESP32 board.
7.  **Monitor Serial**: Open the serial monitor to observe connection status and command logs (uncomment `#define ENABLE_DEBUG` for more verbose output).

## Code Walkthrough

### Defines and Pin Assignments
```cpp
#define WIFI_SSID         "YOUR-WIFI-SSID"    
#define WIFI_PASS         "YOUR-WIFI-PASSWORD"
#define APP_KEY           "YOUR-APP-KEY"      
#define APP_SECRET        "YOUR-APP-SECRET"   
#define SWITCH_ID         "YOUR-DEVICE-ID"    
#define BAUD_RATE         115200                

#if defined(ESP8266)
  #define RELAY_PIN         D5                  // Pin where the relay is connected (D5 = GPIO 14 on ESP8266)
#elif defined(ESP32) || defined(ARDUINO_ARCH_RP2040)
  #define RELAY_PIN         16                  // Pin where the relay is connected (GPIO 16 on ESP32)
#endif
```
These define your Wi-Fi and SinricPro credentials, the switch device ID, serial baud rate, and the GPIO pin for the relay.

### Callbacks
This function is invoked when a corresponding command is received from the SinricPro server.

#### `onPowerState(const String &deviceId, bool &state)`
Handles `setPowerState` commands (on/off). It directly controls the `RELAY_PIN` by writing the `state` (true for HIGH, false for LOW).
```cpp
bool onPowerState(const String &deviceId, bool &state) {
  digitalWrite(RELAY_PIN, state);             
  return true;                                
}
```

### `setup()`
Standard Arduino setup function for initialization.
```cpp
void setup() {
  pinMode(RELAY_PIN, OUTPUT);                 

  #if defined(ESP8266)
    WiFi.setSleepMode(WIFI_NONE_SLEEP); 
    WiFi.setAutoReconnect(true);
  #elif defined(ESP32)
    WiFi.setSleep(false); 
    WiFi.setAutoReconnect(true);
  #endif
  
  WiFi.begin(WIFI_SSID, WIFI_PASS);           
  while (WiFi.status() != WL_CONNECTED) {
    delay(250);
  }
  
  SinricProSwitch& mySwitch = SinricPro[SWITCH_ID];   
  mySwitch.onPowerState(onPowerState);                
  SinricPro.begin(APP_KEY, APP_SECRET);               
}
```
This function initializes the `RELAY_PIN` as an output, connects to Wi-Fi, creates a `SinricProSwitch` instance, registers the `onPowerState` callback, and starts the SinricPro client.

### `loop()`
Standard Arduino loop function for continuous execution.
```cpp
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
`SinricPro`, `Example`, `Relay`, `Switch`, `Smart Home`, `Basic`, `ESP8266`, `ESP32`
