# SinricProWindowAC

## Overview
The `SinricProWindowAC` class represents a smart Window Air Conditioner device within the SinricPro ecosystem. It provides comprehensive control over AC functions, including power, thermostat settings (target temperature, mode), and a generic range value (which can be used for fan speed, swing, etc.). This class aggregates functionalities from various controller capabilities.

## Inheritance
`SinricProWindowAC` inherits from:
*   [`SinricProDevice`](./class-SinricProDevice.md)
*   [`SettingController<SinricProWindowAC>`](./capability-SettingController.md)
*   [`PushNotification<SinricProWindowAC>`](./capability-PushNotification.md)
*   [`PowerStateController<SinricProWindowAC>`](./capability-PowerStateController.md)
*   [`RangeController<SinricProWindowAC>`](./capability-RangeController.md)
*   [`ThermostatController<SinricProWindowAC>`](./capability-ThermostatController.md)

## Constructor

### `SinricProWindowAC(const String &deviceId)`
*   **Purpose**: Constructs a new `SinricProWindowAC` object.
*   **Parameters**:
    *   `deviceId`: A `const String&` containing the unique identifier for this Window AC device.
*   **Return Type**: None
*   **Initialization**: Initializes the base `SinricProDevice` with the provided `deviceId` and sets its product type to `"AC_UNIT"`.

## Capabilities
By inheriting from the following controllers, `SinricProWindowAC` gains their respective functionalities:

*   **PowerStateController**: Enables the AC unit to respond to `setPowerState` commands (on/off) and send `setPowerState` events to report its current status. See [`PowerStateController` documentation](./capability-PowerStateController.md) for details.

*   **ThermostatController**: Enables setting and adjusting target temperatures, and changing thermostat modes (e.g., `"AUTO"`, `"COOL"`, `"HEAT"`). It also allows sending events to report these states. See [`ThermostatController` documentation](./capability-ThermostatController.md) for details.

*   **RangeController**: Allows setting a generic range value (e.g., fan speed, swing level) and adjusting it relatively. It also enables sending events to report the current range value. See [`RangeController` documentation](./capability-RangeController.md) for details.

*   **SettingController**: Allows the AC unit to receive and process generic `setSetting` commands for device-specific configurations. See [`SettingController` documentation](./capability-SettingController.md) for details.

*   **PushNotification**: Provides the ability for the AC unit to send push notifications to the SinricPro App (e.g., for temperature alerts or mode changes). See [`PushNotification` documentation](./capability-PushNotification.md) for details.

## Usage Example
```cpp
#include <SinricPro.h>
#include <SinricProWindowAC.h>

#define APP_KEY    "YOUR_APP_KEY"
#define APP_SECRET "YOUR_APP_SECRET"
#define AC_ID  "YOUR_AC_UNIT_DEVICE_ID"

// Callback for power state changes
bool onPowerState(const String &deviceId, bool &state) {
  Serial.printf("Window AC %s turned %s\r\n", deviceId.c_str(), state ? "ON" : "OFF");
  // Your code to control the physical AC power
  return true;
}

// Callback for thermostat mode changes
bool onThermostatMode(const String &deviceId, String &mode) {
  Serial.printf("Window AC %s set mode to %s\r\n", deviceId.c_str(), mode.c_str());
  // Your code to set the physical AC mode
  return true;
}

// Callback for target temperature changes
bool onTargetTemperature(const String &deviceId, float &temperature) {
  Serial.printf("Window AC %s set target temperature to %.1f°C\r\n", deviceId.c_str(), temperature);
  // Your code to set the physical target temperature
  return true;
}

// Callback for range value changes (e.g., fan speed)
bool onRangeValue(const String &deviceId, int &rangeValue) {
  Serial.printf("Window AC %s set range value to %d\r\n", deviceId.c_str(), rangeValue);
  // Your code to set the AC fan speed or other range-based setting
  return true;
}

void setup() {
  Serial.begin(115200);
  SinricPro.begin(APP_KEY, APP_SECRET);

  // Get a reference to your Window AC device
  SinricProWindowAC &myAC = SinricPro[AC_ID];

  // Set callbacks
  myAC.onPowerState(onPowerState);
  myAC.onThermostatMode(onThermostatMode);
  myAC.onTargetTemperature(onTargetTemperature);
  myAC.onRangeValue(onRangeValue);

  // You can also set callbacks for settings or push notifications if needed
}

void loop() {
  SinricPro.handle();

  // Example: Send current target temperature if changed locally
  // myAC.sendTargetTemperatureEvent(24.0);
}
```

## Dependencies
*   `SinricProDevice.h`
*   `Capabilities/SettingController.h`
*   `Capabilities/PushNotification.h`
*   `Capabilities/PowerStateController.h`
*   `Capabilities/RangeController.h`
*   `Capabilities/ThermostatController.h`

## Tags
`SinricPro`, `SDK`, `Device`, `Window AC`, `Air Conditioner`, `Smart Home`, `HVAC`, `PowerState`, `Thermostat`, `Range`, `Settings`, `PushNotification`, `ESP8266`, `ESP32`
