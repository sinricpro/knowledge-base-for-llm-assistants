# RTSP Camera Example

## Overview
This example demonstrates how to set up an ESP32-CAM board for RTSP (Real-Time Streaming Protocol) video streaming and integrate it with SinricPro for remote snapshot capture and power state control. It utilizes the Micro-RTSP library to provide a live video feed accessible via RTSP-compatible players like VLC.

## Key Concepts/Capabilities Demonstrated
*   **Device Type**: `SinricProCamera`
*   **Capabilities Used**: 
    *   [`PowerStateController`]: For managing the camera's on/off state (logical).
    *   [`CameraController`]: For handling `getSnapshot` requests and sending captured image data to SinricPro.
*   **ESP32-CAM Integration**: Camera initialization and configuration.
*   **RTSP Streaming**: Implementation of RTSP server using the Micro-RTSP library.
*   **SinricPro Integration**: Demonstrates how to initialize SinricPro, register device-specific callbacks, and maintain the connection.
*   **WiFi Connectivity**: Standard ESP-based WiFi connection setup, with an option for static IP configuration.

## Hardware Requirements
*   An ESP32-CAM board (e.g., AI-Thinker ESP32-CAM, ESP-EYE, M5STACK ESP32-CAM variants).
*   (Optional) PSRAM on the board for higher resolution images and better performance.

## Setup Instructions
1.  **Install Libraries**: Ensure you have the SinricPro library and its dependencies installed. You will also need the ESP32 board definitions. Install the `Micro-RTSP` library (available on GitHub: `https://github.com/geeksville/Micro-RTSP`).
2.  **Select Camera Model**: In the `rtsp-camera.ino` sketch, uncomment the `#define CAMERA_MODEL_...` line that corresponds to your specific ESP32-CAM board.
3.  **Update Credentials**: Modify the following `#define` statements in the `.ino` file:
    *   `WIFI_SSID`: Your Wi-Fi network SSID.
    *   `WIFI_PASSWD`: Your Wi-Fi network password.
    *   `APP_KEY`: Your SinricPro App Key (from the SinricPro portal).
    *   `APP_SECRET`: Your SinricPro App Secret (from the SinricPro portal).
    *   `CAMERA_ID`: The Device ID of your Camera, created in the SinricPro portal (ensure it's of type `CAMERA`).
4.  **Static IP (Optional)**: If you wish to use a static IP address for your ESP32-CAM, uncomment `#define ENABLE_STATIC_IP` and configure the `local_IP`, `gateway`, `subnet`, `primaryDNS`, and `secondaryDNS` variables with your network settings.
5.  **Baud Rate**: Adjust `BAUD_RATE` if needed for your serial monitor.
6.  **Upload Sketch**: Upload the `rtsp-camera.ino` sketch to your ESP32-CAM board.
7.  **Monitor Serial**: Open the serial monitor to observe connection status, the assigned IP address, and debug messages. The serial output will provide the RTSP URL (e.g., `rtsp://<ESP_IP_ADDRESS>:8554/mjpeg/1`) which you can use in VLC Media Player or other compatible RTSP clients.

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

// Optional static IP configuration
// #define ENABLE_STATIC_IP
// IPAddress local_IP(192, 168, 1, 124);
// IPAddress gateway(192, 168, 1, 1);
// IPAddress subnet(255, 255, 255, 0);
// IPAddress primaryDNS(8, 8, 8, 8);    
// IPAddress secondaryDNS(8, 8, 4, 4);  
```
These define your Wi-Fi and SinricPro credentials, the camera device ID, serial baud rate, and include `camera_pins.h`. Optional static IP settings are also provided.

### Global Objects
```cpp
OV2640 cam;
CStreamer *streamer;
WiFiServer rtspServer(8554);
```
These are global objects for camera control, RTSP streaming, and the RTSP server.

### Callbacks
These functions are invoked when a corresponding command is received from the SinricPro server.

#### `onPowerState(const String &deviceId, bool &state)`
Handles `setPowerState` commands (on/off). In this example, it primarily logs the state change.
```cpp
bool onPowerState(const String &deviceId, bool &state) {
  Serial.printf("Device %s turned %s (via SinricPro) \r\n", deviceId.c_str(), state ? "on" : "off");
  return true;  
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
  SinricProCamera &myCamera = SinricPro[CAMERA_ID];
  myCamera.onPowerState(onPowerState);
  myCamera.onSnapshot(onSnapshot);
  SinricPro.onConnected([]() { Serial.printf("Connected to SinricPro\r\n"); });
  SinricPro.onDisconnected([]() { Serial.printf("Disconnected from SinricPro\r\n"); });
  SinricPro.begin(APP_KEY, APP_SECRET);
}
```

### `setupWiFi()`
Connects the ESP32-CAM to your specified Wi-Fi network, optionally configuring a static IP address.
```cpp
void setupWiFi() {
  // ... (static IP configuration if ENABLE_STATIC_IP is defined)
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

### `setupCamera()`
Configures and initializes the ESP32 camera module. This function sets parameters like pixel format, frame size, JPEG quality, and handles PSRAM availability.
```cpp
void setupCamera() {
  camera_config_t config;
  // ... (pin assignments and configuration)
  cam.init(config);
}
```

### `setupStreaming()`
Initializes the RTSP server on port 8554 and creates an `OV2640Streamer` object to handle the video stream.
```cpp
void setupStreaming() {
  rtspServer.begin();
  streamer = new OV2640Streamer(cam);  
  Serial.print("Use 'rtsp://");
  Serial.print(WiFi.localIP());
  Serial.println(":8554/mjpeg/1' to stream via VLC");
}
```

### `handleStreaming()`
Manages the RTSP streaming process. It continuously checks for new frames from the camera and streams them to any connected RTSP clients. It also accepts new client connections to the RTSP server.
```cpp
void handleStreaming() {
  uint32_t msecPerFrame = 100;
  static uint32_t lastimage = millis();
  streamer->handleRequests(0);  
  uint32_t now = millis();
  if (streamer->anySessions()) {
    if (now > lastimage + msecPerFrame || now < lastimage) {
      streamer->streamImage(now);
      lastimage = now;
    }
  }
  WiFiClient rtspClient = rtspServer.accept();
  if (rtspClient) {
    Serial.print("client: ");
    Serial.print(rtspClient.remoteIP());
    Serial.println();
    streamer->addSession(rtspClient);
  }
}
```

### `setup()` and `loop()`
Standard Arduino functions for initialization and continuous execution.
```cpp
void setup() {
  Serial.begin(BAUD_RATE);
  while (!Serial);  
  setupCamera();
  setupWiFi();
  setupSinricPro();
  setupStreaming();
}

void loop() {
  SinricPro.handle();
  handleStreaming();
}
```
The `loop()` function continuously calls `SinricPro.handle()` to process SinricPro events and `handleStreaming()` to manage the RTSP video stream.

## Dependencies
*   `Arduino.h`
*   `WiFi.h`
*   `WebServer.h`
*   `WiFiClient.h`
*   `SinricPro.h`
*   `SinricProCamera.h`
*   `Micro-RTSP` library (specifically `SimStreamer.h`, `OV2640Streamer.h`, `CRtspSession.h`)
*   `camera_pins.h`

## Tags
`SinricPro`, `Example`, `Camera`, `ESP32-CAM`, `RTSP`, `Streaming`, `Snapshot`, `PowerState`, `Smart Home`, `Security`, `ESP32`
