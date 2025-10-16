# ContactSensor Example

## Overview
This example demonstrates how to integrate a physical contact sensor (e.g., a magnetic door/window sensor) with SinricPro. It continuously monitors the state of the sensor and sends `setContactState` events to the SinricPro server whenever the contact state changes (open or closed). This is a fundamental example for building smart home security or monitoring systems.

## Key Concepts/Capabilities Demonstrated
*   **Device Type**: `SinricProContactsensor`
*   **Capabilities Used**:
    *   [`ContactSensor`]: For sending `setContactState` events.
*   **Digital Input**: Reading the state of a digital pin connected to the sensor.
*   **Debouncing**: Implementing a simple software debounce to prevent false triggers from sensor noise.
*   **SinricPro Integration**: Demonstrates how to initialize SinricPro, set up connection status callbacks, and maintain the connection.
*   **WiFi Connectivity**: Standard ESP-based WiFi connection setup.

## Hardware Requirements
*   An ESP8266 or ESP32 development board.
*   A contact sensor (e.g., a magnetic reed switch) connected to a digital input pin.

## Setup Instructions
1.  **Install Libraries**: Ensure you have the SinricPro library and its dependencies installed. Refer to the main SDK documentation for installation instructions.
2.  **Update Credentials**: Modify the following `#define` statements in the `ContactSensor.ino` file:
    *   `WIFI_SSID`: Your Wi-Fi network SSID.
    *   `WIFI_PASS`: Your Wi-Fi network password.
    *   `APP_KEY`: Your SinricPro App Key (from the SinricPro portal).
    *   `APP_SECRET`: Your SinricPro App Secret (from the SinricPro portal).
    *   `CONTACT_ID`: The Device ID of your Contact Sensor, created in the SinricPro portal (ensure it's of type `CONTACT_SENSOR`).
3.  **Pin Configuration**: Define `CONTACT_PIN` to the GPIO pin where your contact sensor is connected. The example assumes `LOW` for an open contact and `HIGH` for a closed contact.
4.  **Baud Rate**: Adjust `BAUD_RATE` if needed for your serial monitor.
5.  **Upload Sketch**: Upload the `ContactSensor.ino` sketch to your ESP8266/ESP32 board.
6.  **Monitor Serial**: Open the serial monitor to observe connection status and event logs (uncomment `#define ENABLE_DEBUG` for more verbose output).

## Code Walkthrough

### Defines and Pin Assignments
```cpp
#define WIFI_SSID         "YOUR-WIFI-SSID"
#define WIFI_PASS         "YOUR-WIFI-PASSWORD"
#define APP_KEY           "YOUR-APP-KEY"      
#define APP_SECRET        "YOUR-APP_SECRET"   
#define CONTACT_ID        "YOUR-DEVICE-ID"    
#define BAUD_RATE         115200                

#define CONTACT_PIN       5                   // PIN where contactsensor is connected to
                                              // LOW  = contact is open
                                              // HIGH = contact is closed

bool lastContactState = false;
unsigned long lastChange = 0;
```
These define your Wi-Fi and SinricPro credentials, the contact sensor device ID, serial baud rate, and the GPIO pin for the sensor. `lastContactState` and `lastChange` are used for debouncing.

### `handleContactsensor()`
This function continuously monitors the state of the contact sensor. It implements a debounce delay to prevent rapid, false state changes due to mechanical bounce. When a stable change in the contact sensor's state is detected, it sends a `setContactState` event to the SinricPro server.
```cpp
void handleContactsensor() {
  unsigned long actualMillis = millis();
  if (actualMillis - lastChange < 250) return;          

  bool actualContactState = digitalRead(CONTACT_PIN);   

  if (actualContactState != lastContactState) {         
    Serial.printf("Contactsensor is %s now\r\n", actualContactState?"open":"closed");
    lastContactState = actualContactState;              
    lastChange = actualMillis;                          
    SinricProContactsensor &myContact = SinricPro[CONTACT_ID]; 
    bool success = myContact.sendContactEvent(actualContactState);      
    if(!success) {
      Serial.printf("Something went wrong...could not send Event to server!\r\n");
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
Initializes the SinricPro library and adds the Contact Sensor device. It also sets up callbacks for connection status.
```cpp
void setupSinricPro() {
  SinricProContactsensor& myContact = SinricPro[CONTACT_ID];
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
  pinMode(CONTACT_PIN, INPUT);
  setupWiFi();
  setupSinricPro();
}

void loop() {
  handleContactsensor();
  SinricPro.handle();
}
```
The `loop()` function continuously calls `handleContactsensor()` to monitor the sensor and `SinricPro.handle()` to process SinricPro events and maintain the connection.

## Dependencies
*   `Arduino.h`
*   `ESP8266WiFi.h` (for ESP8266) or `WiFi.h` (for ESP32/RP2040)
*   `SinricPro.h`
*   `SinricProContactsensor.h`

## Tags
`SinricPro`, `Example`, `Contact Sensor`, `Sensor`, `Smart Home`, `Door Sensor`, `Window Sensor`, `Debounce`, `ESP8266`, `ESP32`
