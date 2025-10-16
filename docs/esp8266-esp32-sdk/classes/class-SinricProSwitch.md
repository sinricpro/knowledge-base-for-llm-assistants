# SinricProSwitch

## Overview
The `SinricProSwitch` class represents a smart switch device within the SinricPro ecosystem. It is designed to handle basic on/off commands and can also manage device settings and send push notifications. This class primarily aggregates functionalities from various controller capabilities.

## Inheritance
`SinricProSwitch` inherits from:
*   [`SinricProDevice`](./class-SinricProDevice.md) (Base device class - *Note: Documentation for SinricProDevice is pending*)
*   [`SettingController<SinricProSwitch>`](./capability-SettingController.md)
*   [`PushNotification<SinricProSwitch>`](./capability-PushNotification.md)
*   [`PowerStateController<SinricProSwitch>`](./capability-PowerStateController.md)

## Constructor

### `SinricProSwitch(const String &deviceId)`
*   **Purpose**: Constructs a new `SinricProSwitch` object.
*   **Parameters**:
    *   `deviceId`: A `const String&` containing the unique identifier for this switch device.
*   **Return Type**: None
*   **Initialization**: Initializes the base `SinricProDevice` with the provided `deviceId` and sets its product type to `"SWITCH"`.

## Capabilities
By inheriting from the following controllers, `SinricProSwitch` gains their respective functionalities:

*   **PowerStateController**: Enables the switch to respond to `setPowerState` commands (on/off) and send `setPowerState` events to report its current status. See [`PowerStateController` documentation](./capability-PowerStateController.md) for details.

*   **SettingController**: Allows the switch to receive and process generic `setSetting` commands for device-specific configurations. See [`SettingController` documentation](./capability-SettingController.md) for details.

*   **PushNotification**: Provides the ability for the switch to send push notifications to the SinricPro App. See [`PushNotification` documentation](./capability-PushNotification.md) for details.

## Usage Example
```cpp
#include <SinricPro.h>
#include <SinricProSwitch.h>

#define APP_KEY    "YOUR_APP_KEY"
#define APP_SECRET "YOUR_APP_SECRET"
#define SWITCH_ID  "YOUR_SWITCH_DEVICE_ID"

// Callback for power state changes
bool onPowerState(const String &deviceId, bool &state) {
  Serial.printf("Device %s turned %s\r\n", deviceId.c_str(), state ? "ON" : "OFF");
  // Your code to control the physical switch (e.g., digitalWrite)
  return true; // Indicate success
}

void setup() {
  Serial.begin(115200);
  SinricPro.begin(APP_KEY, APP_SECRET);

  // Get a reference to your switch device
  SinricProSwitch &mySwitch = SinricPro[SWITCH_ID];

  // Set the callback for power state changes
  mySwitch.onPowerState(onPowerState);

  // You can also set callbacks for settings or push notifications if needed
  // mySwitch.onSetSetting(onSetting);
  // mySwitch.onButtonPress(onButtonPress); // If using a virtual button
}

void loop() {
  SinricPro.handle();
}
```

## Dependencies
*   `SinricProDevice.h`
*   `Capabilities/SettingController.h`
*   `Capabilities/PushNotification.h`
*   `Capabilities/PowerStateController.h`

## Tags
`SinricPro`, `SDK`, `Device`, `Switch`, `Smart Home`, `PowerState`, `Settings`, `PushNotification`, `ESP8266`, `ESP32`
