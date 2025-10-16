# MotionSensor Example

## Overview
This example demonstrates how to integrate a physical motion sensor (e.g., a PIR sensor) with SinricPro. It continuously monitors the state of the sensor and sends `motion` events to the SinricPro server whenever motion is detected or no longer detected. This is a fundamental example for building smart home security or presence detection systems.

## Key Concepts/Capabilities Demonstrated
*   **Device Type**: `SinricProMotionsensor`
*   **Capabilities Used**:
    *   [`MotionSensor`]: For sending `motion` events.
*   **Digital Input**: Reading the state of a digital pin connected to the sensor.
*   **Debouncing**: Implementing a simple software debounce to prevent false triggers from sensor noise.
*   **SinricPro Integration**: Demonstrates how to initialize SinricPro, set up connection status callbacks, and maintain the connection.
*   **WiFi Connectivity**: Standard ESP-based WiFi connection setup.

## Hardware Requirements
*   An ESP8266 or ESP32 development board.
*   A motion sensor (e.g., PIR sensor) connected to a digital input pin.

## Setup Instructions
1.  **Install Libraries**: Ensure you have the SinricPro library and its dependencies installed. Refer to the main SDK documentation for installation instructions.
2.  **Update Credentials**: Modify the following `#define` statements in the `MotionSensor.ino` file:
    *   `WIFI_SSID`: Your Wi-Fi network SSID.
    *   `WIFI_PASS`: Your Wi-Fi network password.
    *   `APP_KEY`: Your SinricPro App Key (from the SinricPro portal).
    *   `APP_SECRET`: Your SinricPro App Secret (from the SinricPro portal).
    *   `MOTIONSENSOR_ID`: The Device ID of your Motion Sensor, created in the SinricPro portal (ensure it's of type `MOTION_SENSOR`).
3.  **Pin Configuration**: Define `MOTIONSENSOR_PIN` to the GPIO pin where your motion sensor is connected. The example assumes `HIGH` for motion detected and `LOW` for no motion.
4.  **Baud Rate**: Adjust `BAUD_RATE` if needed for your serial monitor.
5.  **Upload Sketch**: Upload the `MotionSensor.ino` sketch to your ESP8266/ESP32 board.
6.  **Monitor Serial**: Open the serial monitor to observe connection status and event logs (uncomment `#define ENABLE_DEBUG` for more verbose output).

## Code Walkthrough

### Defines and Pin Assignments
```cpp
#define WIFI_SSID         "YOUR-WIFI-SSID"
#define WIFI_PASS         "YOUR-WIFI-PASSWORD"
#define APP_KEY           "YOUR-APP-KEY"      
#define APP_SECRET        "YOUR-APP-SECRET"   
#define MOTIONSENSOR_ID   "YOUR-DEVICE-ID"    
#define BAUD_RATE         115200                

#define MOTIONSENSOR_PIN  5                   // PIN where motionsensor is connected to
                                              // LOW  = motion is not detected
                                              // HIGH = motion is detected

bool lastMotionState = false;
unsigned long lastChange = 0;
```
These define your Wi-Fi and SinricPro credentials, the motion sensor device ID, serial baud rate, and the GPIO pin for the sensor. `lastMotionState` and `lastChange` are used for debouncing.

### `handleMotionsensor()`
This function continuously monitors the state of the motion sensor. It implements a debounce delay to prevent rapid, false state changes. When a stable change in the motion sensor's state is detected, it sends a `motion` event to the SinricPro server.
```cpp
void handleMotionsensor() {
  unsigned long actualMillis = millis();
  if (actualMillis - lastChange < 250) return;          

  bool actualMotionState = digitalRead(MOTIONSENSOR_PIN);   

  if (actualMotionState != lastMotionState) {         
    Serial.printf("Motion %s\r\n", actualMotionState?"detected":"not detected");
    lastMotionState = actualMotionState;              
    lastChange = actualMillis;                        
    SinricProMotionsensor &myMotionsensor = SinricPro[MOTIONSENSOR_ID]; 
    bool success = myMotionsensor.sendMotionEvent(actualMotionState);
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
Initializes the SinricPro library and adds the Motion Sensor device. It also sets up callbacks for connection status.
```cpp
void setupSinricPro() {
  SinricProMotionsensor& myMotionsensor = SinricPro[MOTIONSENSOR_ID];
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
  pinMode(MOTIONSENSOR_PIN, INPUT);
  setupWiFi();
  setupSinricPro();
}

void loop() {
  handleMotionsensor();
  SinricPro.handle();
}
```
The `loop()` function continuously calls `handleMotionsensor()` to monitor the sensor and `SinricPro.handle()` to process SinricPro events and maintain the connection.

## Dependencies
*   `Arduino.h`
*   `ESP8266WiFi.h` (for ESP8266) or `WiFi.h` (for ESP32/RP2040)
*   `SinricPro.h`
*   `SinricProMotionsensor.h`

## Tags
`SinricPro`, `Example`, `Motion Sensor`, `PIR Sensor`, `Sensor`, `Smart Home`, `Security`, `ESP8266`, `ESP32`
