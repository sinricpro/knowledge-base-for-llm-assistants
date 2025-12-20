# Switch Example

## Overview
This example demonstrates how to control a single smart switch (e.g., a relay connected to a light or appliance) using SinricPro. It provides both remote control via the SinricPro platform and local manual control using a physical button. A key feature is the synchronization of the device's state back to the SinricPro server when controlled manually, ensuring the app always reflects the true status of the device.

## Key Concepts/Capabilities Demonstrated
*   **Device Type**: `SinricProSwitch`
*   **Capabilities Used**: 
    *   [`PowerStateController`]: For managing the switch's on/off state.
*   **Digital Output Control**: Toggling a GPIO pin to control a physical LED (representing a relay or appliance).
*   **Digital Input Reading**: Monitoring a physical button press.
*   **Button Debouncing**: Implementing a simple software debounce to prevent false triggers from button noise.
*   **State Synchronization**: Ensuring the reported state in SinricPro matches the physical state of the device, whether controlled remotely or locally.
*   **SinricPro Integration**: Demonstrates how to initialize SinricPro, register device-specific callbacks, and maintain the connection.
*   **WiFi Connectivity**: Standard ESP-based WiFi connection setup.

## Hardware Requirements
*   An ESP8266 or ESP32 development board.
*   A momentary push button (e.g., the built-in flash button on NodeMCU, or an external button).
*   An LED (e.g., the built-in LED on ESP boards) or a relay to represent the switchable device.

## Setup Instructions
1.  **Install Libraries**: Ensure you have the SinricPro library and its dependencies installed. Refer to the main SDK documentation for installation instructions.
2.  **Update Credentials**: Modify the following `#define` statements in the `Switch.ino` file:
    *   `WIFI_SSID`: Your Wi-Fi network SSID.
    *   `WIFI_PASS`: Your Wi-Fi network password.
    *   `APP_KEY`: Your SinricPro App Key (from the SinricPro portal).
    *   `APP_SECRET`: Your SinricPro App Secret (from the SinricPro portal).
    *   `SWITCH_ID`: The Device ID of your Switch, created in the SinricPro portal (ensure it's of type `SWITCH`).
3.  **Pin Configuration**: 
    *   `BUTTON_PIN`: Define the GPIO pin for your push button (default is 0, often the flash button on ESP8266). Connect one side of the button to `BUTTON_PIN` and the other to GND (using `INPUT_PULLUP`).
    *   `LED_PIN`: Define the GPIO pin for your LED or relay (default is 2, often the built-in LED on ESP8266). Connect your LED with a current-limiting resistor to `LED_PIN` and GND, or connect your relay module.
4.  **Baud Rate**: Adjust `BAUD_RATE` if needed for your serial monitor.
5.  **Wiring**: Connect your button and LED/relay to the defined GPIO pins as described above.
6.  **Upload Sketch**: Upload the `Switch.ino` sketch to your ESP8266/ESP32 board.
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

#define BUTTON_PIN 0   // GPIO for BUTTON (inverted: LOW = pressed, HIGH = released)
#define LED_PIN   2   // GPIO for LED (inverted)

bool myPowerState = false;
unsigned long lastBtnPress = 0;
```
These define your Wi-Fi and SinricPro credentials, the switch device ID, serial baud rate, and the GPIO pins for the button and LED. `myPowerState` tracks the current state of the switch, and `lastBtnPress` is used for debouncing the button input.

### Callbacks
This function is invoked when a corresponding command is received from the SinricPro server.

#### `onPowerState(const String &deviceId, bool &state)`
Handles `setPowerState` requests from SinricPro. It prints the requested state, updates `myPowerState`, and controls the `LED_PIN` (or relay) accordingly.
```cpp
bool onPowerState(const String &deviceId, bool &state) {
  Serial.printf("Device %s turned %s (via SinricPro) \r\n", deviceId.c_str(), state?"on":"off");
  myPowerState = state;
  digitalWrite(LED_PIN, myPowerState?LOW:HIGH); // Assuming inverted logic for LED_BUILTIN
  return true; 
}
```

### `handleButtonPress()`
This function continuously monitors the `BUTTON_PIN`. It implements a debounce delay (1000ms) to prevent false triggers. When the button is pressed (pin reads `LOW`), it toggles `myPowerState`, controls the `LED_PIN`, and then calls `mySwitch.sendPowerStateEvent(myPowerState)` to report the new state to SinricPro, ensuring synchronization.
```cpp
void handleButtonPress() {
  unsigned long actualMillis = millis(); 
  if (digitalRead(BUTTON_PIN) == LOW && actualMillis - lastBtnPress > 1000)  {
    if (myPowerState) {     
      myPowerState = false;
    } else {
      myPowerState = true;
    }
    digitalWrite(LED_PIN, myPowerState?LOW:HIGH); 

    SinricProSwitch& mySwitch = SinricPro[SWITCH_ID];
    bool success = mySwitch.sendPowerStateEvent(myPowerState); 
    if(!success) {
      Serial.printf("Something went wrong...could not send Event to server!\r\n");
    }

    Serial.printf("Device %s turned %s (manually via flashbutton)\r\n", mySwitch.getDeviceId().c_str(), myPowerState?"on":"off");

    lastBtnPress = actualMillis;  
  } 
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
Initializes the SinricPro library and adds the Switch device. It registers the `onPowerState` callback and sets up connection status callbacks.
```cpp
void setupSinricPro() {
  SinricProSwitch& mySwitch = SinricPro[SWITCH_ID];
  mySwitch.onPowerState(onPowerState);

  SinricPro.onConnected([](){ Serial.printf("Connected to SinricPro\r\n"); }); 
  SinricPro.onDisconnected([](){ Serial.printf("Disconnected from SinricPro\r\n"); });
  SinricPro.begin(APP_KEY, APP_SECRET);
}
```

### `setup()` and `loop()`
Standard Arduino functions for initialization and continuous execution.
```cpp
void setup() {
  pinMode(BUTTON_PIN, INPUT_PULLUP); 
  pinMode(LED_PIN, OUTPUT); 
  digitalWrite(LED_PIN, HIGH); // turn off LED on bootup (assuming inverted logic)

  Serial.begin(BAUD_RATE); Serial.printf("\r\n\r\n");
  setupWiFi();
  setupSinricPro();
}

void loop() {
  handleButtonPress();
  SinricPro.handle();
}
```

The `loop()` function continuously calls `handleButtonPress()` to monitor the button and `SinricPro.handle()` to process SinricPro events and maintain the connection.

## Dependencies
*   `Arduino.h`
*   `ESP8266WiFi.h` (for ESP8266) or `WiFi.h` (for ESP32/RP2040)
*   `SinricPro.h`
*   `SinricProSwitch.h`

## Tags
`SinricPro`, `Example`, `Switch`, `Relay`, `Button`, `Local Control`, `State Synchronization`, `Smart Home`, `Basic`, `ESP8266`, `ESP32`
