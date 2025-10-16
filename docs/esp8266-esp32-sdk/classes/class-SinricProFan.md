# SinricProFan

## Overview
The `SinricProFan` class represents a smart fan device within the SinricPro ecosystem. It is designed to handle basic on/off commands and allows for speed control by setting a power level. This class aggregates functionalities from various controller capabilities.

## Inheritance
`SinricProFan` inherits from:
*   [`SinricProDevice`](./class-SinricProDevice.md)
*   [`SettingController<SinricProFan>`](./capability-SettingController.md)
*   [`PushNotification<SinricProFan>`](./capability-PushNotification.md)
*   [`PowerStateController<SinricProFan>`](./capability-PowerStateController.md)
*   [`PowerLevelController<SinricProFan>`](./capability-PowerLevelController.md)

## Constructor

### `SinricProFan(const String &deviceId)`
*   **Purpose**: Constructs a new `SinricProFan` object.
*   **Parameters**:
    *   `deviceId`: A `const String&` containing the unique identifier for this fan device.
*   **Return Type**: None
*   **Initialization**: Initializes the base `SinricProDevice` with the provided `deviceId` and sets its product type to `"FAN_NON-US"`.

## Capabilities
By inheriting from the following controllers, `SinricProFan` gains their respective functionalities:

*   **PowerStateController**: Enables the fan to respond to `setPowerState` commands (on/off) and send `setPowerState` events to report its current status. See [`PowerStateController` documentation](./capability-PowerStateController.md) for details.

*   **PowerLevelController**: Allows setting an absolute power level (fan speed) and adjusting it relatively. It also enables sending events to report the current power level. See [`PowerLevelController` documentation](./capability-PowerLevelController.md) for details.

*   **SettingController**: Allows the fan to receive and process generic `setSetting` commands for device-specific configurations. See [`SettingController` documentation](./capability-SettingController.md) for details.

*   **PushNotification**: Provides the ability for the fan to send push notifications to the SinricPro App. See [`PushNotification` documentation](./capability-PushNotification.md) for details.

## Usage Example
```cpp
#include <SinricPro.h>
#include <SinricProFan.h>

#define APP_KEY    "YOUR_APP_KEY"
#define APP_SECRET "YOUR_APP_SECRET"
#define FAN_ID  "YOUR_FAN_DEVICE_ID"

// Callback for power state changes
bool onPowerState(const String &deviceId, bool &state) {
  Serial.printf("Fan %s turned %s\r\n", deviceId.c_str(), state ? "ON" : "OFF");
  // Your code to control the physical fan power
  return true;
}

// Callback for power level changes (fan speed)
bool onPowerLevel(const String &deviceId, int &powerLevel) {
  Serial.printf("Fan %s set speed to %d\r\n", deviceId.c_str(), powerLevel);
  // Your code to set the fan speed (e.g., PWM control)
  return true;
}

void setup() {
  Serial.begin(115200);
  SinricPro.begin(APP_KEY, APP_SECRET);

  // Get a reference to your fan device
  SinricProFan &myFan = SinricPro[FAN_ID];

  // Set callbacks
  myFan.onPowerState(onPowerState);
  myFan.onPowerLevel(onPowerLevel);

  // You can also set callbacks for settings or push notifications if needed
}

void loop() {
  SinricPro.handle();

  // Example: Send current fan speed if changed locally
  // myFan.sendPowerLevelEvent(75); // Set to 75% speed
}
```

## Dependencies
*   `SinricProDevice.h`
*   `Capabilities/SettingController.h`
*   `Capabilities/PushNotification.h`
*   `Capabilities/PowerStateController.h`
*   `Capabilities/PowerLevelController.h`

## Tags
`SinricPro`, `SDK`, `Device`, `Fan`, `Smart Home`, `PowerState`, `PowerLevel`, `Settings`, `PushNotification`, `ESP8266`, `ESP32`
