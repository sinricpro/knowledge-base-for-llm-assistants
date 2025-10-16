# SinricProDimSwitch

## Overview
The `SinricProDimSwitch` class represents a dimmable switch device within the SinricPro ecosystem. It is designed to handle basic on/off commands and also allows for dimming control (setting a power level). This class aggregates functionalities from various controller capabilities.

## Inheritance
`SinricProDimSwitch` inherits from:
*   [`SinricProDevice`](./class-SinricProDevice.md)
*   [`SettingController<SinricProDimSwitch>`](./capability-SettingController.md)
*   [`PushNotification<SinricProDimSwitch>`](./capability-PushNotification.md)
*   [`PowerStateController<SinricProDimSwitch>`](./capability-PowerStateController.md)
*   [`PowerLevelController<SinricProDimSwitch>`](./capability-PowerLevelController.md)

## Constructor

### `SinricProDimSwitch(const String &deviceId)`
*   **Purpose**: Constructs a new `SinricProDimSwitch` object.
*   **Parameters**:
    *   `deviceId`: A `const String&` containing the unique identifier for this dimmable switch device.
*   **Return Type**: None
*   **Initialization**: Initializes the base `SinricProDevice` with the provided `deviceId` and sets its product type to `"DIMMABLE_SWITCH"`.

## Capabilities
By inheriting from the following controllers, `SinricProDimSwitch` gains their respective functionalities:

*   **PowerStateController**: Enables the dimmable switch to respond to `setPowerState` commands (on/off) and send `setPowerState` events to report its current status. See [`PowerStateController` documentation](./capability-PowerStateController.md) for details.

*   **PowerLevelController**: Allows setting an absolute power level (dimming) and adjusting it relatively. It also enables sending events to report the current power level. See [`PowerLevelController` documentation](./capability-PowerLevelController.md) for details.

*   **SettingController**: Allows the dimmable switch to receive and process generic `setSetting` commands for device-specific configurations. See [`SettingController` documentation](./capability-SettingController.md) for details.

*   **PushNotification**: Provides the ability for the dimmable switch to send push notifications to the SinricPro App. See [`PushNotification` documentation](./capability-PushNotification.md) for details.

## Usage Example
```cpp
#include <SinricPro.h>
#include <SinricProDimSwitch.h>

#define APP_KEY    "YOUR_APP_KEY"
#define APP_SECRET "YOUR_APP_SECRET"
#define DIMSWITCH_ID  "YOUR_DIMMABLE_SWITCH_DEVICE_ID"

// Callback for power state changes
bool onPowerState(const String &deviceId, bool &state) {
  Serial.printf("DimSwitch %s turned %s\r\n", deviceId.c_str(), state ? "ON" : "OFF");
  // Your code to control the physical switch (e.g., turn LED on/off)
  return true;
}

// Callback for power level changes (dimming)
bool onPowerLevel(const String &deviceId, int &powerLevel) {
  Serial.printf("DimSwitch %s set power level to %d\r\n", deviceId.c_str(), powerLevel);
  // Your code to set the dimming level (e.g., analogWrite or PWM)
  return true;
}

void setup() {
  Serial.begin(115200);
  SinricPro.begin(APP_KEY, APP_SECRET);

  // Get a reference to your dimmable switch device
  SinricProDimSwitch &myDimSwitch = SinricPro[DIMSWITCH_ID];

  // Set callbacks
  myDimSwitch.onPowerState(onPowerState);
  myDimSwitch.onPowerLevel(onPowerLevel);

  // You can also set callbacks for settings or push notifications if needed
}

void loop() {
  SinricPro.handle();

  // Example: Send current power level if changed locally
  // myDimSwitch.sendPowerLevelEvent(50); // Set to 50% brightness
}
```

## Dependencies
*   `SinricProDevice.h`
*   `Capabilities/SettingController.h`
*   `Capabilities/PushNotification.h`
*   `Capabilities/PowerStateController.h`
*   `Capabilities/PowerLevelController.h`

## Tags
`SinricPro`, `SDK`, `Device`, `Dimmer`, `Switch`, `Dimmable Switch`, `Smart Home`, `PowerState`, `PowerLevel`, `Settings`, `PushNotification`, `ESP8266`, `ESP32`
