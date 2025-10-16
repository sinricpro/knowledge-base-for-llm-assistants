# GarageDoor Example

## Overview
This example demonstrates how to integrate a smart garage door with SinricPro. It allows for remote control of the garage door, enabling it to be opened or closed via the SinricPro platform. This is a foundational example for building smart garage door openers.

## Key Concepts/Capabilities Demonstrated
*   **Device Type**: `SinricProGarageDoor`
*   **Capabilities Used**:
    *   [`DoorController`]: For handling `setMode` commands (open/close) for the garage door.
*   **SinricPro Integration**: Demonstrates how to initialize SinricPro, register device-specific callbacks, and maintain the connection.
*   **WiFi Connectivity**: Standard ESP-based WiFi connection setup.

## Hardware Requirements
*   An ESP8266 or ESP32 development board.
*   (Implied) A mechanism to control a physical garage door (e.g., a relay connected to the garage door opener's control circuit).

## Setup Instructions
1.  **Install Libraries**: Ensure you have the SinricPro library and its dependencies installed. Refer to the main SDK documentation for installation instructions.
2.  **Update Credentials**: Modify the following `#define` statements in the `GarageDoor.ino` file:
    *   `WIFI_SSID`: Your Wi-Fi network SSID.
    *   `WIFI_PASS`: Your Wi-Fi network password.
    *   `APP_KEY`: Your SinricPro App Key (from the SinricPro portal).
    *   `APP_SECRET`: Your SinricPro App Secret (from the SinricPro portal).
    *   `GARAGEDOOR_ID`: The Device ID of your Garage Door, created in the SinricPro portal (ensure it's of type `GARAGE_DOOR`).
3.  **Baud Rate**: Adjust `BAUD_RATE` if needed for your serial monitor.
4.  **Upload Sketch**: Upload the `GarageDoor.ino` sketch to your ESP8266/ESP32 board.
5.  **Monitor Serial**: Open the serial monitor to observe connection status and command logs (uncomment `#define ENABLE_DEBUG` for more verbose output).

## Code Walkthrough

### Defines
```cpp
#define WIFI_SSID         "YOUR_WIFI_SSID"    
#define WIFI_PASS         "YOUR_WIFI_PASSWORD"
#define APP_KEY           "YOUR_APP_KEY_HERE"      
#define APP_SECRET        "YOUR_APP_SECRET_HERE"   
#define GARAGEDOOR_ID     "YOUR_DEVICE_ID_HERE"    
#define BAUD_RATE         115200                     
```
These define your Wi-Fi and SinricPro credentials, the garage door device ID, and serial baud rate.

### Callbacks
This function is invoked when a corresponding command is received from the SinricPro server.

#### `onDoorState(const String& deviceId, bool &doorState)`
Handles `setMode` commands for the garage door. `doorState` is `true` for a request to close, and `false` for a request to open.
```cpp
bool onDoorState(const String& deviceId, bool &doorState) {
  Serial.printf("Garagedoor is %s now.\r\n", doorState?"closed":"open");
  // Your code to control the physical garage door (e.g., activate a relay)
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
Initializes the SinricPro library and registers the necessary callbacks for the Garage Door device.
```cpp
void setupSinricPro() {
  SinricProGarageDoor &myGarageDoor = SinricPro[GARAGEDOOR_ID];
  myGarageDoor.onDoorState(onDoorState);

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

## Dependencies
*   `Arduino.h`
*   `ESP8266WiFi.h` (for ESP8266) or `WiFi.h` (for ESP32/RP2040)
*   `SinricPro.h`
*   `SinricProGarageDoor.h`

## Tags
`SinricPro`, `Example`, `Garage Door`, `Door Control`, `Smart Home`, `ESP8266`, `ESP32`
