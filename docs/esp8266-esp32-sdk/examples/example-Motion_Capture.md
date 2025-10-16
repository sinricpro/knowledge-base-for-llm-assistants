# Motion Capture Example

## Overview
This example demonstrates how to build a motion-activated camera system using an ESP32-CAM board and integrate it with SinricPro. When motion is detected by a PIR sensor, the system captures a series of frames, saves them temporarily to the SPIFFS file system, and then sends this motion data to the SinricPro server. It also incorporates debouncing for the motion sensor and a cooldown period to prevent excessive data transmission.

## Key Concepts/Capabilities Demonstrated
*   **Device Type**: `SinricProCamera`
*   **Capabilities Used**:
    *   [`CameraController`]: Specifically for sending motion data (`sendMotion`).
*   **ESP32-CAM Integration**: Camera initialization and frame capture using `esp_camera.h`.
*   **PIR Motion Sensor**: Interfacing with a digital motion sensor.
*   **SPIFFS**: Utilizing the Flash File System for temporary storage of captured frames.
*   **SinricPro Integration**: Demonstrates how to initialize SinricPro and maintain the connection.
*   **WiFi Connectivity**: Standard ESP-based WiFi connection setup.
*   **Debouncing**: Implementing software debouncing for sensor input.
*   **Event Rate Limiting**: Using a cooldown period to control the frequency of data transmission.

## Hardware Requirements
*   An ESP32-CAM board (e.g., AI-Thinker ESP32-CAM, ESP-EYE, M5STACK ESP32-CAM variants).
*   PIR Motion Sensor (e.g., HC-SR501).
*   (Optional) PSRAM on the board for higher resolution images and better performance.

## Setup Instructions
1.  **Install Libraries**: Ensure you have the SinricPro library and its dependencies installed. You will also need the ESP32 board definitions in your Arduino IDE or PlatformIO setup.
2.  **Select Camera Model**: In the `motion-capture.ino` sketch, uncomment the `#define CAMERA_MODEL_...` line that corresponds to your specific ESP32-CAM board.
3.  **Update Credentials**: Modify the following `#define` statements in the `.ino` file:
    *   `WIFI_SSID`: Your Wi-Fi network SSID.
    *   `WIFI_PASS`: Your Wi-Fi network password.
    *   `APP_KEY`: Your SinricPro App Key (from the SinricPro portal).
    *   `APP_SECRET`: Your SinricPro App Secret (from the SinricPro portal).
    *   `CAMERA_ID`: The Device ID of your Camera, created in the SinricPro portal (ensure it's of type `CAMERA`).
4.  **Wiring**: Connect your PIR motion sensor to the `MOTION_SENSOR_PIN` defined in the sketch (default is GPIO 5). Ensure proper power (VCC, GND) for the sensor.
5.  **SPIFFS Upload**: This example uses SPIFFS for temporary file storage. Ensure you have uploaded the SPIFFS filesystem to your ESP32-CAM board. You can typically do this using the ESP32 Sketch Data Upload tool in Arduino IDE.
6.  **Upload Sketch**: Upload the `motion-capture.ino` sketch to your ESP32-CAM board.
7.  **Monitor Serial**: Open the serial monitor to observe connection status, motion detection events, and data transmission logs (debug output is enabled by default).

## Code Walkthrough

### Defines and Pin Assignments
```cpp
#define MOTION_SENSOR_PIN 5          // GPIO pin connected to PIR motion sensor
#define FRAME_CAPTURE_COUNT 10       // Number of frames to capture when motion is detected
#define FRAME_CAPTURE_DELAY_MS 200   // Delay between frame captures (milliseconds)
#define MOTION_DEBOUNCE_DELAY 250    // Debounce delay for motion sensor (milliseconds)
#define TEMP_FILE_PATH "/mjpeg.tmp"  // Temporary file path in SPIFFS for storing frames

#define WIFI_SSID ""   
#define WIFI_PASS ""   
#define APP_KEY ""     
#define APP_SECRET ""  
#define CAMERA_ID ""   

bool lastMotionState = false;               
unsigned long lastChangeTime = 0;           
unsigned long lastSendTime = 0;             
const unsigned long SEND_COOLDOWN = 60000;  // Minimum time between sends (60 seconds)
```
These define the pin for the motion sensor, parameters for frame capture, debouncing, temporary file path, Wi-Fi and SinricPro credentials, and variables for managing motion state and cooldown.

### `initFS()`
Initializes the SPIFFS file system, which is used to temporarily store captured frames before sending.
```cpp
bool initFS() {
  if (!SPIFFS.begin(true)) {
    Serial.println("SPIFFS initialization failed!");
    return false;
  }
  return true;
}
```

### `initCamera()`
Configures and initializes the ESP32 camera module. This function sets parameters like pixel format, frame size, JPEG quality, and handles PSRAM availability.
```cpp
bool initCamera() {
  camera_config_t config;
  // ... (pin assignments and configuration)
  esp_err_t err = esp_camera_init(&config);
  if (err != ESP_OK) {
    Serial.printf("Camera init failed with error 0x%x", err);
    return false;
  }
  // ... (sensor settings)
  return true;
}
```

### `setupWiFi()`
Connects the ESP32-CAM to your specified Wi-Fi network.
```cpp
void setupWiFi() {
  Serial.print("\nConnecting to WiFi");
  WiFi.begin(WIFI_SSID, WIFI_PASS);
  WiFi.setSleep(false); 
  while (WiFi.status() != WL_CONNECTED) {
    Serial.print(".");
    delay(250);
  }
  Serial.printf("\nWiFi connected! IP address: %s\n", WiFi.localIP().toString().c_str());
}
```

### `setupSinricPro()`
Initializes the SinricPro library, adds the Camera device, and sets up connection status callbacks. It also includes basic `onPowerState` and `onSnapshot` callbacks, though the primary focus of this example is motion data.
```cpp
void setupSinricPro() {
  SinricProCamera& camera = SinricPro[CAMERA_ID];
  // ... (onPowerState and onSnapshot callbacks)
  SinricPro.onConnected([]() { Serial.println("Connected to SinricPro"); });
  SinricPro.onDisconnected([]() { Serial.println("Disconnected from SinricPro"); });
  SinricPro.begin(APP_KEY, APP_SECRET);
}
```

### `captureFrames(File& file)`
Captures a predefined number of frames (`FRAME_CAPTURE_COUNT`) from the camera and writes them sequentially to the provided `File` object in SPIFFS.
```cpp
bool captureFrames(File& file) {
  // ... (frame capture and writing logic)
}
```

### `sendMotionToServer()`
Reads the captured frames from the temporary file in SPIFFS and sends them to the SinricPro server using `camera.sendMotion()`. It handles various HTTP response codes.
```cpp
void sendMotionToServer() {
  SinricProCamera& camera = SinricPro[CAMERA_ID];
  int responseCode = camera.sendMotion(SPIFFS, TEMP_FILE_PATH);
  // ... (response code handling)
}
```

### `captureAndSendMotion()`
This function orchestrates the motion capture and sending process. It first checks if the `SEND_COOLDOWN` period has passed since the last transmission. If allowed, it opens a temporary file, calls `captureFrames` to fill it, then `sendMotionToServer`, and finally removes the temporary file.
```cpp
void captureAndSendMotion() {
  // ... (cooldown check)
  File mjpegFile = SPIFFS.open(TEMP_FILE_PATH, FILE_WRITE);
  // ... (captureFrames, sendMotionToServer, SPIFFS.remove)
}
```

### `handleMotionSensor()`
This function continuously reads the state of the PIR sensor. It implements debouncing to prevent false triggers and calls `captureAndSendMotion()` when a change from no motion to motion is detected.
```cpp
void handleMotionSensor() {
  // ... (debounce logic)
  bool currentMotionState = digitalRead(MOTION_SENSOR_PIN);
  if (currentMotionState != lastMotionState) {
    // ... (trigger captureAndSendMotion if motion detected)
  }
}
```

### `setup()` and `loop()`
Standard Arduino functions for initialization and continuous execution.
```cpp
void setup() {
  Serial.begin(115200);
  if (!initFS()) return;
  pinMode(MOTION_SENSOR_PIN, INPUT);
  if (!initCamera()) return;
  setupWiFi();
  setupSinricPro();
}

void loop() {
  SinricPro.handle();
  handleMotionSensor();
}
```
The `loop()` function continuously calls `SinricPro.handle()` to process events and `handleMotionSensor()` to monitor for motion.

## Dependencies
*   `Arduino.h`
*   `WiFi.h`
*   `SinricPro.h`
*   `SinricProCamera.h`
*   `SPIFFS.h`
*   `esp_camera.h`
*   `esp_http_client.h`
*   `HTTPClient.h`
*   `WiFiClientSecure.h`
*   `camera_pins.h`

## Tags
`SinricPro`, `Example`, `Camera`, `Motion Detection`, `PIR Sensor`, `ESP32-CAM`, `SPIFFS`, `Smart Home`, `Security`, `ESP32`
