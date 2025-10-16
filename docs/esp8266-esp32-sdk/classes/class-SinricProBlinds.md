# SinricProBlinds

## Overview
The `SinricProBlinds` class represents a smart blinds device within the SinricPro ecosystem. It is designed to control interior blinds, supporting basic on/off commands, setting the position (0-100% open), and explicit open/close commands. This class aggregates functionalities from various controller capabilities.

## Inheritance
`SinricProBlinds` inherits from:
*   [`SinricProDevice`](./class-SinricProDevice.md)
*   [`SettingController<SinricProBlinds>`](./capability-SettingController.md)
*   [`PushNotification<SinricProBlinds>`](./capability-PushNotification.md)
*   [`PowerStateController<SinricProBlinds>`](./capability-PowerStateController.md)
*   [`RangeController<SinricProBlinds>`](./capability-RangeController.md)

## Constructor

### `SinricProBlinds(const String &deviceId)`
*   **Purpose**: Constructs a new `SinricProBlinds` object.
*   **Parameters**:
    *   `deviceId`: A `const String&` containing the unique identifier for this blinds device.
*   **Return Type**: None
*   **Initialization**: Initializes the base `SinricProDevice` with the provided `deviceId` and sets its product type to `"BLIND"`.

## Capabilities
By inheriting from the following controllers, `SinricProBlinds` gains their respective functionalities:

*   **PowerStateController**: Enables the blinds to respond to `setPowerState` commands (on/off) and send `setPowerState` events to report its current status. See [`PowerStateController` documentation](./capability-PowerStateController.md) for details.

*   **RangeController**: Allows setting the blinds' open percentage (0-100%) and adjusting it relatively. It also enables sending events to report the current position. See [`RangeController` documentation](./capability-RangeController.md) for details.

*   **SettingController**: Allows the blinds to receive and process generic `setSetting` commands for device-specific configurations. See [`SettingController` documentation](./capability-SettingController.md) for details.

*   **PushNotification**: Provides the ability for the blinds to send push notifications to the SinricPro App (e.g., for status updates or errors). See [`PushNotification` documentation](./capability-PushNotification.md) for details.

## Usage Example
```cpp
#include <SinricPro.h>
#include <SinricProBlinds.h>

#define APP_KEY    "YOUR_APP_KEY"
#define APP_SECRET "YOUR_APP_SECRET"
#define BLINDS_ID  "YOUR_BLINDS_DEVICE_ID"

// Callback for power state changes
bool onPowerState(const String &deviceId, bool &state) {
  Serial.printf("Blinds %s turned %s\r\n", deviceId.c_str(), state ? "ON" : "OFF");
  // Your code to control the physical blinds power
  return true;
}

// Callback for setting blinds position
bool onSetPosition(const String &deviceId, int &position) {
  Serial.printf("Blinds %s set position to %d%%\r\n", deviceId.c_str(), position);
  // Your code to move the blinds to the specified position
  return true;
}

void setup() {
  Serial.begin(115200);
  SinricPro.begin(APP_KEY, APP_SECRET);

  // Get a reference to your blinds device
  SinricProBlinds &myBlinds = SinricPro[BLINDS_ID];

  // Set callbacks
  myBlinds.onPowerState(onPowerState);
  myBlinds.onRangeValue(onSetPosition); // RangeController handles position

  // You can also set callbacks for settings or push notifications if needed
}

void loop() {
  SinricPro.handle();

  // Example: Send current blinds position every now and then
  // myBlinds.sendRangeValueEvent(50); // Blinds are 50% open
}
```

## Dependencies
*   `SinricProDevice.h`
*   `Capabilities/SettingController.h`
*   `Capabilities/PushNotification.h`
*   `Capabilities/RangeController.h`
*   `Capabilities/PowerStateController.h`

## Tags
`SinricPro`, `SDK`, `Device`, `Blinds`, `Curtains`, `Smart Home`, `PowerState`, `Range`, `Settings`, `PushNotification`, `ESP8266`, `ESP32`
