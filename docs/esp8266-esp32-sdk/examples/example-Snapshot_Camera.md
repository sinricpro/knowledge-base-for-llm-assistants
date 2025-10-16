# Snapshot Camera Example

## Overview
This example demonstrates how to use an ESP32-CAM board to capture still images (snapshots) and send them to the SinricPro server upon request. It provides a straightforward implementation for a remote camera that can take pictures on demand, making it suitable for simple monitoring or security applications.

## Key Concepts/Capabilities Demonstrated
*   **Device Type**: `SinricProCamera`
*   **Capabilities Used**: 
    *   [`CameraController`]: Specifically for handling `getSnapshot` requests and sending captured image data (`sendSnapshot`).
*   **ESP32-CAM Integration**: Camera initialization and frame capture using `esp_camera.h`.
*   **SinricPro Integration**: Demonstrates how to initialize SinricPro, register device-specific callbacks, and maintain the connection.
*   **WiFi Connectivity**: Standard ESP-based WiFi connection setup.

## Hardware Requirements
*   An ESP32-CAM board (e.g., AI-Thinker ESP32-CAM, ESP-EYE, M5STACK ESP32-CAM variants).
*   (Optional) PSRAM on the board for higher resolution images.

## Setup Instructions
1.  **Install Libraries**: Ensure you have the SinricPro library and its dependencies installed. You will also need the ESP32 board definitions in your Arduino IDE or PlatformIO setup.
2.  **Select Camera Model**: In the `snapshot-camera.ino` sketch, uncomment the `#define CAMERA_MODEL_...` line that corresponds to your specific ESP32-CAM board. This ensures correct pin mappings.
3.  **Update Credentials**: Modify the following `#define` statements in the `.ino` file:
    *   `WIFI_SSID`: Your Wi-Fi network SSID.
    *   `WIFI_PASS`: Your Wi-Fi network password.
    *   `APP_KEY`: Your SinricPro App Key (from the SinricPro portal).
    *   `APP_SECRET`: Your SinricPro App Secret (from the SinricPro portal).
    *   `CAMERA_ID`: The Device ID of your Camera, created in the SinricPro portal (ensure it's of type `CAMERA`).
4.  **Baud Rate**: Adjust `BAUD_RATE` if needed for your serial monitor.
5.  **Upload Sketch**: Upload the `snapshot-camera.ino` sketch to your ESP32-CAM board.
6.  **Monitor Serial**: Open the serial monitor to observe connection status and debug messages (uncomment `#define ENABLE_DEBUG` for more verbose output).

## Code Walkthrough

### Defines and Camera Model Selection
```cpp
#define WIFI_SSID ""   
#define WIFI_PASS ""   
#define APP_KEY    "" 
#define APP_SECRET "" 
#define CAMERA_ID  "" 
#define BAUD_RATE 115200  

// Select camera model (example: #define CAMERA_MODEL_ESP_EYE)
#include "camera_pins.h"
```
These define your Wi-Fi and SinricPro credentials, the camera device ID, serial baud rate, and include `camera_pins.h` which contains pin definitions for various ESP32-CAM models. You must uncomment the line corresponding to your camera model.

### Callbacks
These functions are invoked when a corresponding command is received from the SinricPro server.

#### `onSnapshot(const String &deviceId)`
Handles `getSnapshot` requests. It captures a frame from the camera and sends it to SinricPro.
```cpp
bool onSnapshot(const String &deviceId) {
  camera_fb_t *fb = esp_camera_fb_get();
  if (!fb) {
    Serial.println("Failed to grab image");
    return false;
  }
  SinricProCamera &myCamera = SinricPro[deviceId];
  int responseCode = myCamera.sendSnapshot(fb->buf, fb->len);
  // Handle different response codes
  switch (responseCode) {
    case 200:
      Serial.println("Snapshot sent successfully");
      break;
    case 413:
      Serial.println("Error: File exceeds maximum allowed size");
      break;
    case 429:
      Serial.println("Error: Rate limit exceeded");
      break;
    default:
      Serial.printf("Error: Send failed with code %d\n", responseCode);
      break;
  }
  esp_camera_fb_return(fb);
  return responseCode == 200;
}
```

#### `onPowerState(const String &deviceId, bool &state)`
Basic callback for `setPowerState` requests. It simply returns `true`, indicating the request was handled, but doesn't implement physical power control.
```cpp
bool onPowerState(const String &deviceId, bool &state) {
  return true;
}
```

### `setupWiFi()`
Connects the ESP32-CAM to your specified Wi-Fi network.
```cpp
void setupWiFi() {
  Serial.printf("\r\n[Wifi]: Connecting");
  WiFi.begin(WIFI_SSID, WIFI_PASS);
  WiFi.setSleep(false);
  while (WiFi.status() != WL_CONNECTED) {
    Serial.printf(".");
    delay(250);
  }
  Serial.printf("connected!\r\n[WiFi]: IP-Address is %s\r\n", WiFi.localIP().toString().c_str());
}
```

### `setupSinricPro()`
Initializes the SinricPro library, adds the Camera device, and registers the necessary callbacks.
```cpp
void setupSinricPro() {
  SinricProCamera &myCamera = SinricPro[CAMERA_ID];
  myCamera.onPowerState(onPowerState);
  myCamera.onSnapshot(onSnapshot);
  SinricPro.onConnected([]() { Serial.printf("Connected to SinricPro\r\n"); });
  SinricPro.onDisconnected([]() { Serial.printf("Disconnected from SinricPro\r\n"); });
  SinricPro.begin(APP_KEY, APP_SECRET);
}
```

### `setupCamera()`
Configures and initializes the ESP32 camera module. This function sets parameters like pixel format, frame size, JPEG quality, and handles PSRAM availability.
```cpp
void setupCamera() {
  camera_config_t config;
  // ... (pin assignments and configuration) 
  esp_err_t err = esp_camera_init(&config);
  if (err != ESP_OK) {
    Serial.printf("Camera init failed with error 0x%x", err);
    return;
  }
  // ... (sensor settings)
}
```

### `setup()` and `loop()`
Standard Arduino functions for initialization and continuous execution.
```cpp
void setup() {
  Serial.begin(BAUD_RATE);
  setupCamera();
  setupWiFi();
  setupSinricPro();  
}

void loop() {
  SinricPro.handle();
}
```
The `loop()` function continuously calls `SinricPro.handle()` to process SinricPro events and maintain connection.

## Dependencies
*   `Arduino.h`
*   `WiFi.h`
*   `SinricPro.h`
*   `SinricProCamera.h`
*   `esp_camera.h`
*   `camera_pins.h`

## Tags
`SinricPro`, `Example`, `Camera`, `ESP32-CAM`, `Snapshot`, `Still Image`, `PowerState`, `Smart Home`, `Security`, `ESP32`
