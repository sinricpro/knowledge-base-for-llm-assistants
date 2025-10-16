# MJPEG Camera Example

## Overview
This example demonstrates how to transform an ESP32-CAM board into a smart camera integrated with SinricPro. It enables MJPEG video streaming accessible via a web browser or VLC, and allows for remote snapshot capture through the SinricPro platform. This is a comprehensive solution for building a smart security camera or remote monitoring system.

## Key Concepts/Capabilities Demonstrated
*   **Device Type**: `SinricProCamera`
*   **Capabilities Used**:
    *   [`PowerStateController`]: For managing the camera's on/off state (logical, not necessarily physical power).
    *   [`CameraController`]: For handling `getSnapshot` requests and sending captured image data to SinricPro.
*   **ESP32-CAM Integration**: Detailed camera initialization and configuration using `esp_camera.h`.
*   **MJPEG Streaming**: Setting up a web server for live video streaming.
*   **SinricPro Integration**: Demonstrates how to initialize SinricPro, register device-specific callbacks, and maintain the connection.
*   **WiFi Connectivity**: Standard ESP-based WiFi connection setup.

## Hardware Requirements
*   An ESP32-CAM board (e.g., AI-Thinker ESP32-CAM, ESP-EYE, M5STACK ESP32-CAM variants).
*   (Optional) PSRAM on the board for higher resolution images and better performance.

## Setup Instructions
1.  **Install Libraries**: Ensure you have the SinricPro library and its dependencies installed. You will also need the ESP32 board definitions in your Arduino IDE or PlatformIO setup.
2.  **Select Camera Model**: In the `mjpeg-camera.ino` sketch, uncomment the `#define CAMERA_MODEL_...` line that corresponds to your specific ESP32-CAM board. This ensures correct pin mappings.
3.  **Update Credentials**: Modify the following `#define` statements in the `.ino` file:
    *   `WIFI_SSID`: Your Wi-Fi network SSID.
    *   `WIFI_PASSWD`: Your Wi-Fi network password.
    *   `APP_KEY`: Your SinricPro App Key (from the SinricPro portal).
    *   `APP_SECRET`: Your SinricPro App Secret (from the SinricPro portal).
    *   `CAMERA_ID`: The Device ID of your Camera, created in the SinricPro portal (ensure it's of type `CAMERA`).
4.  **Baud Rate**: Adjust `BAUD_RATE` if needed for your serial monitor.
5.  **Upload Sketch**: Upload the `mjpeg-camera.ino` sketch to your ESP32-CAM board. Ensure you select the correct board and COM port.
6.  **Monitor Serial**: Open the serial monitor to observe connection status, the assigned IP address, and debug messages. The serial output will provide URLs to view the MJPEG stream in a web browser or VLC.

## Code Walkthrough

### Defines and Camera Model Selection
```cpp
#define WIFI_SSID         "YOUR-WIFI-SSID"
#define WIFI_PASSWD       "YOUR-WIFI-PASSWORD"
#define APP_KEY           "YOUR-APP-KEY"      
#define APP_SECRET        "YOUR-APP-SECRET"   
#define CAMERA_ID         "YOUR-DEVICE-ID"    
#define BAUD_RATE         115200 

// Select camera model (example: #define CAMERA_MODEL_ESP_EYE)
#include "camera_pins.h" 
```
These define your Wi-Fi and SinricPro credentials, the camera device ID, serial baud rate, and include `camera_pins.h` which contains pin definitions for various ESP32-CAM models. You must uncomment the line corresponding to your camera model.

### `setupWiFi()`
Connects the ESP32-CAM to your specified Wi-Fi network and prints the assigned local IP address.
```cpp
void setupWiFi() {
  IPAddress ip;
  WiFi.setSleep(false); 
  WiFi.setAutoReconnect(true);   
  WiFi.begin(WIFI_SSID, WIFI_PASSWD);
  while (WiFi.status() != WL_CONNECTED) {
      delay(500);
      Serial.print(".");
  }
  Serial.println("");
  Serial.println("WiFi connected");
  Serial.println("IP address: ");
  Serial.println(WiFi.localIP());
}
```

### Callbacks
These functions are invoked when a corresponding command is received from the SinricPro server.

#### `onPowerState(const String &deviceId, bool &state)`
Handles `setPowerState` commands (on/off). In this example, it primarily logs the state change.
```cpp
bool onPowerState(const String &deviceId, bool &state) {
  Serial.printf("Camera %s turned %s (via SinricPro) \r\n", deviceId.c_str(), state?"on":"off");
  return true; // request handled properly
}
```

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
  // ... (error handling and serial prints)
  esp_camera_fb_return(fb);
  return responseCode == 200;
}
```

### `setupSinricPro()`
Initializes the SinricPro library, adds the Camera device, and registers the necessary callbacks.
```cpp
void setupSinricPro() {
  SinricProCamera& myCamera = SinricPro[CAMERA_ID];
  myCamera.onPowerState(onPowerState);
  myCamera.onSnapshot(onSnapshot);
  SinricPro.onConnected([](){ Serial.printf("Connected to SinricPro\r\n"); }); 
  SinricPro.onDisconnected([](){ Serial.printf("Disconnected from SinricPro\r\n"); });
  SinricPro.begin(APP_KEY, APP_SECRET);
}
```

### `setup()` and `loop()`
Standard Arduino functions for initialization and continuous execution.
```cpp
void setup() {
  Serial.begin(BAUD_RATE);
  Serial.setDebugOutput(true);
  Serial.println();

  setupWiFi();
  setupSinricPro();  
  cameraInit();   
  startCameraServer(); // Implementation in app_httpd.cpp
  
  Serial.print("Use 'http://"); Serial.print(WiFi.localIP()); Serial.println("' to view via web");
  Serial.print("Use 'http://"); Serial.print(WiFi.localIP()); Serial.println(":81/stream' to stream via VLC");
}

void loop() {
  delay(20); // Small delay, main work is event-driven or in background tasks
}
```

### `cameraInit()`
Configures and initializes the ESP32 camera module. This function sets parameters like pixel format, frame size, JPEG quality, and handles PSRAM availability.
```cpp
void cameraInit(void){
  camera_config_t config;
  // ... (pin assignments and configuration)
  config.frame_size = FRAMESIZE_UXGA;
  config.pixel_format = PIXFORMAT_JPEG; // for streaming
  // ... (PSRAM checks and adjustments)
  esp_err_t err = esp_camera_init(&config);
  if (err != ESP_OK) {
    Serial.printf("Camera init failed with error 0x%x", err);
    return;
  }
  // ... (sensor settings and LED flash setup)
}
```

### `startCameraServer()`
*Note: The implementation of `startCameraServer()` is not directly in this `.ino` file but is typically found in `app_httpd.cpp` within the same example directory.* This function sets up the HTTP server that serves the MJPEG video stream and handles web requests for the camera.

## Dependencies
*   `Arduino.h`
*   `esp_camera.h`
*   `WiFi.h`
*   `SinricPro.h`
*   `SinricProCamera.h`
*   `camera_pins.h`
*   `app_httpd.cpp` (for `startCameraServer` implementation)

## Tags
`SinricPro`, `Example`, `Camera`, `ESP32-CAM`, `MJPEG`, `Streaming`, `Snapshot`, `PowerState`, `Smart Home`, `Security`, `ESP32`
