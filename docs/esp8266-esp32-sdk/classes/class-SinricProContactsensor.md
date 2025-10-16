# SinricProContactsensor

## Overview
The `SinricProContactsensor` class represents a contact sensor device within the SinricPro ecosystem. It is designed to report events related to the state of a contact sensor, such as whether a door or window is open or closed. This class aggregates functionalities from various controller capabilities.

## Inheritance
`SinricProContactsensor` inherits from:
*   [`SinricProDevice`](./class-SinricProDevice.md)
*   [`SettingController<SinricProContactsensor>`](./capability-SettingController.md)
*   [`PushNotification<SinricProContactsensor>`](./capability-PushNotification.md)
*   [`ContactSensor<SinricProContactsensor>`](./capability-ContactSensor.md)

## Constructor

### `SinricProContactsensor(const String &deviceId)`
*   **Purpose**: Constructs a new `SinricProContactsensor` object.
*   **Parameters**:
    *   `deviceId`: A `const String&` containing the unique identifier for this contact sensor device.
*   **Return Type**: None
*   **Initialization**: Initializes the base `SinricProDevice` with the provided `deviceId` and sets its product type to `"CONTACT_SENSOR"`.

## Capabilities
By inheriting from the following controllers, `SinricProContactsensor` gains their respective functionalities:

*   **ContactSensor**: Enables the device to send `setContactState` events to report whether the contact is open or closed. See [`ContactSensor` documentation](./capability-ContactSensor.md) for details.

*   **SettingController**: Allows the sensor to receive and process generic `setSetting` commands for device-specific configurations. See [`SettingController` documentation](./capability-SettingController.md) for details.

*   **PushNotification**: Provides the ability for the sensor to send push notifications to the SinricPro App (e.g., for alerts when a contact changes state). See [`PushNotification` documentation](./capability-PushNotification.md) for details.

## Usage Example
```cpp
#include <SinricPro.h>
#include <SinricProContactsensor.h>

#define APP_KEY    "YOUR_APP_KEY"
#define APP_SECRET "YOUR_APP_SECRET"
#define SENSOR_ID  "YOUR_CONTACT_SENSOR_DEVICE_ID"

// Callback for generic settings (optional)
bool onSetSetting(const String &deviceId, const String &settingId, String &settingValue) {
  Serial.printf("Device %s received setting %s = %s\r\n", deviceId.c_str(), settingId.c_str(), settingValue.c_str());
  return true;
}

void setup() {
  Serial.begin(115200);
  SinricPro.begin(APP_KEY, APP_SECRET);

  // Get a reference to your contact sensor device
  SinricProContactsensor &myContactSensor = SinricPro[SENSOR_ID];

  // Set the callback for generic settings
  myContactSensor.onSetSetting(onSetSetting);

  // You can also set callbacks for push notifications if needed
}

void loop() {
  SinricPro.handle();

  // Example: Send contact state event based on a physical sensor
  static bool lastState = false;
  bool currentState = digitalRead(CONTACT_SENSOR_PIN); // Assuming a digital pin for sensor

  if (currentState != lastState) {
    myContactSensor.sendContactEvent(currentState); // true for closed, false for open
    Serial.printf("Sent Contact Event: %s\r\n", currentState ? "CLOSED" : "OPEN");
    lastState = currentState;
    delay(100); // Debounce
  }
}
```

## Dependencies
*   `SinricProDevice.h`
*   `Capabilities/SettingController.h`
*   `Capabilities/PushNotification.h`
*   `Capabilities/ContactSensor.h`

## Tags
`SinricPro`, `SDK`, `Device`, `Contact Sensor`, `Sensor`, `Smart Home`, `Door Sensor`, `Window Sensor`, `ESP8266`, `ESP32`
