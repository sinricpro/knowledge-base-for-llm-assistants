# SinricProMotionsensor

## Overview
The `SinricProMotionsensor` class represents a motion sensor device within the SinricPro ecosystem. It is designed to report motion detection events and can also manage device settings and send push notifications. This class aggregates functionalities from various controller capabilities.

## Inheritance
`SinricProMotionsensor` inherits from:
*   [`SinricProDevice`](./class-SinricProDevice.md)
*   [`SettingController<SinricProMotionsensor>`](./capability-SettingController.md)
*   [`PushNotification<SinricProMotionsensor>`](./capability-PushNotification.md)
*   [`MotionSensor<SinricProMotionsensor>`](./capability-MotionSensor.md)

## Constructor

### `SinricProMotionsensor(const String &deviceId)`
*   **Purpose**: Constructs a new `SinricProMotionsensor` object.
*   **Parameters**:
    *   `deviceId`: A `const String&` containing the unique identifier for this motion sensor device.
*   **Return Type**: None
*   **Initialization**: Initializes the base `SinricProDevice` with the provided `deviceId` and sets its product type to `"MOTION_SENSOR"`.

## Capabilities
By inheriting from the following controllers, `SinricProMotionsensor` gains their respective functionalities:

*   **MotionSensor**: Enables the device to send `motion` events to report whether motion has been detected or not. See [`MotionSensor` documentation](./capability-MotionSensor.md) for details.

*   **SettingController**: Allows the sensor to receive and process generic `setSetting` commands for device-specific configurations. See [`SettingController` documentation](./capability-SettingController.md) for details.

*   **PushNotification**: Provides the ability for the sensor to send push notifications to the SinricPro App (e.g., for motion alerts). See [`PushNotification` documentation](./capability-PushNotification.md) for details.

## Usage Example
```cpp
#include <SinricPro.h>
#include <SinricProMotionsensor.h>

#define APP_KEY    "YOUR_APP_KEY"
#define APP_SECRET "YOUR_APP_SECRET"
#define SENSOR_ID  "YOUR_MOTION_SENSOR_DEVICE_ID"

// Callback for generic settings (optional)
bool onSetSetting(const String &deviceId, const String &settingId, String &settingValue) {
  Serial.printf("Device %s received setting %s = %s\r\n", deviceId.c_str(), settingId.c_str(), settingValue.c_str());
  return true;
}

void setup() {
  Serial.begin(115200);
  SinricPro.begin(APP_KEY, APP_SECRET);

  // Get a reference to your motion sensor device
  SinricProMotionsensor &myMotionSensor = SinricPro[SENSOR_ID];

  // Set the callback for generic settings
  myMotionSensor.onSetSetting(onSetSetting);

  // You can also set callbacks for push notifications if needed
}

void loop() {
  SinricPro.handle();

  // Example: Send motion event based on a physical sensor
  static bool lastMotionState = false;
  bool currentMotionState = digitalRead(MOTION_SENSOR_PIN); // Assuming a digital pin for sensor

  if (currentMotionState != lastMotionState) {
    myMotionSensor.sendMotionEvent(currentMotionState); // true for detected, false for not detected
    Serial.printf("Sent Motion Event: %s\r\n", currentMotionState ? "DETECTED" : "NOT DETECTED");
    lastMotionState = currentMotionState;
    delay(100); // Debounce
  }
}
```

## Dependencies
*   `SinricProDevice.h`
*   `Capabilities/SettingController.h`
*   `Capabilities/PushNotification.h`
*   `Capabilities/MotionSensor.h`

## Tags
`SinricPro`, `SDK`, `Device`, `Motion Sensor`, `Sensor`, `Smart Home`, `Security`, `ESP8266`, `ESP32`
