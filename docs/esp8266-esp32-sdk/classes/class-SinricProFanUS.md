# SinricProFanUS

## Overview
The `SinricProFanUS` class represents a smart fan device, specifically tailored for US-style fan control, within the SinricPro ecosystem. It is designed to handle basic on/off commands and allows for speed control by setting a value within a defined range. This class aggregates functionalities from various controller capabilities.

## Inheritance
`SinricProFanUS` inherits from:
*   [`SinricProDevice`](./class-SinricProDevice.md)
*   [`SettingController<SinricProFanUS>`](./capability-SettingController.md)
*   [`PushNotification<SinricProFanUS>`](./capability-PushNotification.md)
*   [`PowerStateController<SinricProFanUS>`](./capability-PowerStateController.md)
*   [`RangeController<SinricProFanUS>`](./capability-RangeController.md)

## Constructor

### `SinricProFanUS(const String &deviceId)`
*   **Purpose**: Constructs a new `SinricProFanUS` object.
*   **Parameters**:
    *   `deviceId`: A `const String&` containing the unique identifier for this fan device.
*   **Return Type**: None
*   **Initialization**: Initializes the base `SinricProDevice` with the provided `deviceId` and sets its product type to `"FAN"`.

## Capabilities
By inheriting from the following controllers, `SinricProFanUS` gains their respective functionalities:

*   **PowerStateController**: Enables the fan to respond to `setPowerState` commands (on/off) and send `setPowerState` events to report its current status. See [`PowerStateController` documentation](./capability-PowerStateController.md) for details.

*   **RangeController**: Allows setting the fan speed within a defined range and adjusting it relatively. It also enables sending events to report the current speed. This is typically used for fans with multiple discrete speed settings or continuous control. See [`RangeController` documentation](./capability-RangeController.md) for details.

*   **SettingController**: Allows the fan to receive and process generic `setSetting` commands for device-specific configurations. See [`SettingController` documentation](./capability-SettingController.md) for details.

*   **PushNotification**: Provides the ability for the fan to send push notifications to the SinricPro App. See [`PushNotification` documentation](./capability-PushNotification.md) for details.

## Usage Example
```cpp
#include <SinricPro.h>
#include <SinricProFanUS.h>

#define APP_KEY    "YOUR_APP_KEY"
#define APP_SECRET "YOUR_APP_SECRET"
#define FAN_ID  "YOUR_FAN_DEVICE_ID"

// Callback for power state changes
bool onPowerState(const String &deviceId, bool &state) {
  Serial.printf("Fan %s turned %s\r\n", deviceId.c_str(), state ? "ON" : "OFF");
  // Your code to control the physical fan power
  return true;
}

// Callback for range value changes (fan speed)
bool onRangeValue(const String &deviceId, int &rangeValue) {
  Serial.printf("Fan %s set speed to %d\r\n", deviceId.c_str(), rangeValue);
  // Your code to set the fan speed (e.g., control a motor or relay for speed levels)
  return true;
}

void setup() {
  Serial.begin(115200);
  SinricPro.begin(APP_KEY, APP_SECRET);

  // Get a reference to your fan device
  SinricProFanUS &myFan = SinricPro[FAN_ID];

  // Set callbacks
  myFan.onPowerState(onPowerState);
  myFan.onRangeValue(onRangeValue);

  // You can also set callbacks for settings or push notifications if needed
}

void loop() {
  SinricPro.handle();

  // Example: Send current fan speed if changed locally
  // myFan.sendRangeValueEvent(3); // Set to speed level 3
}
```

## Dependencies
*   `SinricProDevice.h`
*   `Capabilities/SettingController.h`
*   `Capabilities/PushNotification.h`
*   `Capabilities/PowerStateController.h`
*   `Capabilities/RangeController.h`

## Tags
`SinricPro`, `SDK`, `Device`, `Fan`, `US Fan`, `Smart Home`, `PowerState`, `Range`, `Settings`, `PushNotification`, `ESP8266`, `ESP32`
