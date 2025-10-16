# MultiSwitch_intermediate Example

## Overview
This example demonstrates a more efficient and scalable approach to controlling multiple SinricPro Switch devices (relays) using a single ESP module, compared to the beginner example. Instead of dedicated callbacks for each switch, it utilizes an array of Device IDs and a single, generic callback function to handle `setPowerState` commands for all switches. This simplifies code management and is suitable for projects with a moderate number of switches.

## Key Concepts/Capabilities Demonstrated
*   **Device Type**: `SinricProSwitch` (multiple instances).
*   **Capabilities Used**: 
    *   [`PowerStateController`]: For managing the on/off state of each individual switch.
*   **Multi-Device Control**: Controlling several smart devices from a single sketch.
*   **Array-based Configuration**: Using a `String` array to store and manage multiple Device IDs.
*   **Single Generic Callback**: Attaching one callback function to handle events for all devices, with logic to identify the specific device within the callback.
*   **SinricPro Integration**: Demonstrates how to initialize SinricPro and maintain the connection for multiple devices.
*   **WiFi Connectivity**: Standard ESP-based WiFi connection setup.

## Hardware Requirements
*   An ESP8266 or ESP32 development board.
*   Multiple relays (e.g., 1-channel, 2-channel, 4-channel relay modules).
*   Devices to be controlled by the relays.

## Setup Instructions
1.  **Install Libraries**: Ensure you have the SinricPro library and its dependencies installed. Refer to the main SDK documentation for installation instructions.
2.  **Update Device IDs Array**: In the `MultiSwitch_intermediate.ino` file, modify the `SWITCH_IDs` array with the actual Device IDs of your `SWITCH` devices created in the SinricPro portal. Ensure the `DEVICES` macro matches the number of IDs in the array.
3.  **Update Credentials**: Modify the following `#define` statements:
    *   `WIFI_SSID`: Your Wi-Fi network SSID.
    *   `WIFI_PASS`: Your Wi-Fi network password.
    *   `APP_KEY`: Your SinricPro App Key.
    *   `APP_SECRET`: Your SinricPro App Secret.
4.  **Baud Rate**: Adjust `BAUD_RATE` if needed for your serial monitor.
5.  **Wiring**: Connect your relays to the GPIO pins you intend to use. Remember to implement the actual `digitalWrite` calls within the `onPowerState` function to control your physical relays (e.g., using an array of pins mapped to `SWITCH_IDs`).
6.  **Upload Sketch**: Upload the `MultiSwitch_intermediate.ino` sketch to your ESP8266/ESP32 board.
7.  **Monitor Serial**: Open the serial monitor to observe connection status and command logs (uncomment `#define ENABLE_DEBUG` for more verbose output).

## Code Walkthrough

### Defines and Device IDs Array
```cpp
#define WIFI_SSID         "YOUR-WIFI-SSID"    
#define WIFI_PASS         "YOUR-WIFI-PASSWORD"
#define APP_KEY           "YOUR-APP-KEY"      
#define APP_SECRET        "YOUR-APP-SECRET"   
#define BAUD_RATE         115200                

#define DEVICES           4                   
String SWITCH_IDs[DEVICES] = {                
  "YOUR_DEVICE_ID_1",
  "YOUR_DEVICE_ID_2",
  "YOUR_DEVICE_ID_3",
  "YOUR_DEVICE_ID_4"
};
```
These define your Wi-Fi and SinricPro credentials, serial baud rate, the number of devices, and an array holding the unique Device IDs for each switch.

### Callbacks
This single callback function handles `setPowerState` requests for *all* configured switches. It iterates through the `SWITCH_IDs` array to identify which device the command is for.

#### `onPowerState(const String &deviceId, bool &state)`
```cpp
bool onPowerState(const String &deviceId, bool &state) {
  for (int i=0; i <DEVICES; i++) {     
    if (deviceId == SWITCH_IDs[i]) {    
      Serial.printf("Device number %i turned %s\r\n", i, state?"on":"off");   
      // Example: Control physical relay based on index 'i'
      // digitalWrite(RELAY_PINS[i], state);
    }
  }
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
Initializes the SinricPro library. It iterates through the `SWITCH_IDs` array, creates a `SinricProSwitch` instance for each Device ID, and registers the single `onPowerState` callback function to all of them.
```cpp
void setupSinricPro() {
  for (int i = 0; i <DEVICES; i++) {
    SinricProSwitch& mySwitch = SinricPro[SWITCH_IDs[i]];
    mySwitch.onPowerState(onPowerState);
  }

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
`SinricPro`, `Example`, `Multi-Switch`, `Switch`, `Relay`, `Intermediate`, `Scalable`, `Smart Home`, `ESP8266`, `ESP32`
