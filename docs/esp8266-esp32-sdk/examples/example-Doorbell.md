# Doorbell Example

## Overview
This example demonstrates how to set up a smart doorbell using SinricPro. It monitors a physical push button connected to an ESP board and sends a `DoorbellPress` event to the SinricPro server whenever the button is activated. This provides a simple yet effective way to integrate a traditional doorbell into a smart home system.

## Key Concepts/Capabilities Demonstrated
*   **Device Type**: `SinricProDoorbell`
*   **Capabilities Used**:
    *   [`Doorbell`]: For sending `DoorbellPress` events.
*   **Digital Input**: Reading the state of a digital pin connected to a push button.
*   **Button Debouncing**: Implementing a simple software debounce to prevent multiple triggers from a single button press.
*   **SinricPro Integration**: Demonstrates how to initialize SinricPro, set up connection status callbacks, and maintain the connection.
*   **WiFi Connectivity**: Standard ESP-based WiFi connection setup.

## Hardware Requirements
*   An ESP8266 or ESP32 development board.
*   A momentary push button.

## Setup Instructions
1.  **Install Libraries**: Ensure you have the SinricPro library and its dependencies installed. Refer to the main SDK documentation for installation instructions.
2.  **Update Credentials**: Modify the following `#define` statements in the `doorbell.ino` file:
    *   `WIFI_SSID`: Your Wi-Fi network SSID.
    *   `WIFI_PASS`: Your Wi-Fi network password.
    *   `APP_KEY`: Your SinricPro App Key (from the SinricPro portal).
    *   `APP_SECRET`: Your SinricPro App Secret (from the SinricPro portal).
    *   `DOORBELL_ID`: The Device ID of your Doorbell, created in the SinricPro portal (ensure it's of type `CONTACT_SENSOR` as per `SinricProDoorbell` definition).
3.  **Pin Configuration**: Define `BUTTON_PIN` to the GPIO pin where your push button is connected. The example uses `INPUT_PULLUP`, so connect one side of the button to the defined pin and the other side to GND. For NodeMCU, GPIO-0 (D3) is often used as a built-in flash button.
4.  **Baud Rate**: Adjust `BAUD_RATE` if needed for your serial monitor.
5.  **Upload Sketch**: Upload the `doorbell.ino` sketch to your ESP8266/ESP32 board.
6.  **Monitor Serial**: Open the serial monitor to observe connection status and event logs (uncomment `#define ENABLE_DEBUG` for more verbose output).

## Code Walkthrough

### Defines and Pin Assignments
```cpp
#define WIFI_SSID         "YOUR-WIFI-SSID"
#define WIFI_PASS         "YOUR-WIFI-PASSWORD"
#define APP_KEY           "YOUR-APP-KEY"      
#define APP_SECRET        "YOUR-APP_SECRET"   
#define DOORBELL_ID       "YOUR-DEVICE-ID"    
#define BAUD_RATE         115200                

#define BUTTON_PIN 0 // change this to your button PIN

static unsigned long lastBtnPress;
```
These define your Wi-Fi and SinricPro credentials, the doorbell device ID, serial baud rate, and the GPIO pin for the button. `lastBtnPress` is used for debouncing the button input.

### `checkButtonPress()`
This function continuously monitors the state of the `BUTTON_PIN`. It implements a simple debounce mechanism to ensure that only one `DoorbellPress` event is sent per physical button press. When the button is pressed (pin reads `LOW`), it sends the event to SinricPro.
```cpp
void checkButtonPress() {
  static unsigned long lastBtnPress;
  unsigned long actualMillis = millis();

  if (actualMillis-lastBtnPress > 500) {
    if (digitalRead(BUTTON_PIN)==LOW) {
      Serial.printf("Ding dong...\r\n");
      lastBtnPress = actualMillis;

      SinricProDoorbell& myDoorbell = SinricPro[DOORBELL_ID];
      bool success = myDoorbell.sendDoorbellEvent();
      if(!success) {
        Serial.printf("Something went wrong...could not send Event to server!\r\n");
      }
    }
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
  IPAddress localIP = WiFi.localIP();
  Serial.printf("connected!\r\n[WiFi]: IP-Address is %d.%d.%d.%d\r\n", localIP[0], localIP[1], localIP[2], localIP[3]);
}
```

### `setupSinricPro()`
Initializes the SinricPro library and adds the Doorbell device. It also sets up callbacks for connection status.
```cpp
void setupSinricPro() {
  SinricProDoorbell& myDoorbell = SinricPro[DOORBELL_ID];
  SinricPro.onConnected([](){ Serial.printf("Connected to SinricPro\r\n"); }); 
  SinricPro.onDisconnected([](){ Serial.printf("Disconnected from SinricPro\r\n"); });
  SinricPro.begin(APP_KEY, APP_SECRET);
}
```

### `setup()` and `loop()`
Standard Arduino functions for initialization and continuous execution.
```cpp
void setup() {
  pinMode(BUTTON_PIN, INPUT_PULLUP); // BUTTIN_PIN as INPUT
  Serial.begin(BAUD_RATE); Serial.printf("\r\n\r\n");
  setupWiFi();
  setupSinricPro();
}

void loop() {
  checkButtonPress();
  SinricPro.handle();
}
```
The `loop()` function continuously calls `checkButtonPress()` to monitor the button and `SinricPro.handle()` to process SinricPro events and maintain the connection.

## Dependencies
*   `Arduino.h`
*   `ESP8266WiFi.h` (for ESP8266) or `WiFi.h` (for ESP32/RP2040)
*   `SinricPro.h`
*   `SinricProDoorbell.h`

## Tags
`SinricPro`, `Example`, `Doorbell`, `Button`, `Sensor`, `Smart Home`, `Debounce`, `ESP8266`, `ESP32`
