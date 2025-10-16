# SinricProCamera

## Overview
The `SinricProCamera` class represents a smart camera device within the SinricPro ecosystem. It is designed to support functionalities such as capturing snapshots, detecting motion, and basic on/off control. This class aggregates functionalities from various controller capabilities.

## Inheritance
`SinricProCamera` inherits from:
*   [`SinricProDevice`](./class-SinricProDevice.md)
*   [`SettingController<SinricProCamera>`](./capability-SettingController.md)
*   [`PushNotification<SinricProCamera>`](./capability-PushNotification.md)
*   [`PowerStateController<SinricProCamera>`](./capability-PowerStateController.md)
*   [`CameraController<SinricProCamera>`](./capability-CameraController.md)

## Constructor

### `SinricProCamera(const String &deviceId)`
*   **Purpose**: Constructs a new `SinricProCamera` object.
*   **Parameters**:
    *   `deviceId`: A `const String&` containing the unique identifier for this camera device.
*   **Return Type**: None
*   **Initialization**: Initializes the base `SinricProDevice` with the provided `deviceId` and sets its product type to `"CAMERA"`.

## Capabilities
By inheriting from the following controllers, `SinricProCamera` gains their respective functionalities:

*   **PowerStateController**: Enables the camera to respond to `setPowerState` commands (on/off) and send `setPowerState` events to report its current status. See [`PowerStateController` documentation](./capability-PowerStateController.md) for details.

*   **CameraController**: Provides the core camera functionalities, including handling `getSnapshot` requests and sending captured images or motion data to the SinricPro server. See [`CameraController` documentation](./capability-CameraController.md) for details.

*   **SettingController**: Allows the camera to receive and process generic `setSetting` commands for device-specific configurations. See [`SettingController` documentation](./capability-SettingController.md) for details.

*   **PushNotification**: Provides the ability for the camera to send push notifications to the SinricPro App (e.g., for motion detection alerts). See [`PushNotification` documentation](./capability-PushNotification.md) for details.

## Usage Example
```cpp
#include <SinricPro.h>
#include <SinricProCamera.h>

#define APP_KEY    "YOUR_APP_KEY"
#define APP_SECRET "YOUR_APP_SECRET"
#define CAMERA_ID  "YOUR_CAMERA_DEVICE_ID"

// Callback for power state changes
bool onPowerState(const String &deviceId, bool &state) {
  Serial.printf("Camera %s turned %s\r\n", deviceId.c_str(), state ? "ON" : "OFF");
  // Your code to power on/off the camera module
  return true;
}

// Callback for snapshot requests
bool onSnapshot(const String &deviceId) {
  Serial.printf("Camera %s received snapshot request\r\n", deviceId.c_str());
  // Your code to capture an image (e.g., from ESP32-CAM)
  // For example, if you have image data in a buffer:
  // uint8_t *imageData = ...;
  // size_t imageLen = ...;
  // SinricProCamera &myCamera = SinricPro[CAMERA_ID];
  // myCamera.sendSnapshot(imageData, imageLen);
  return true;
}

void setup() {
  Serial.begin(115200);
  SinricPro.begin(APP_KEY, APP_SECRET);

  // Get a reference to your camera device
  SinricProCamera &myCamera = SinricPro[CAMERA_ID];

  // Set callbacks
  myCamera.onPowerState(onPowerState);
  myCamera.onSnapshot(onSnapshot);

  // You can also set callbacks for settings or push notifications if needed
}

void loop() {
  SinricPro.handle();

  // Example: Send motion event (if motion detected by your sensor)
  // if (motionDetected) {
  //   myCamera.sendMotion(SPIFFS, "/motion_clip.mp4"); // Example for sending a file
  // }
}
```

## Dependencies
*   `SinricProDevice.h`
*   `Capabilities/SettingController.h`
*   `Capabilities/PushNotification.h`
*   `Capabilities/PowerStateController.h`
*   `Capabilities/CameraController.h`

## Tags
`SinricPro`, `SDK`, `Device`, `Camera`, `Smart Home`, `Snapshot`, `Motion Detection`, `PowerState`, `Settings`, `PushNotification`, `ESP8266`, `ESP32`
