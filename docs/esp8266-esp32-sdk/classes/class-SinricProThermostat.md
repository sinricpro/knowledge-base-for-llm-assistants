# SinricProThermostat

## Overview
The `SinricProThermostat` class represents a smart thermostat device within the SinricPro ecosystem. It provides comprehensive control over heating and cooling, allowing users to set and adjust target temperatures, change thermostat modes (e.g., AUTO, COOL, HEAT), and report the actual ambient temperature. This class aggregates functionalities from various controller capabilities.

## Inheritance
`SinricProThermostat` inherits from:
*   [`SinricProDevice`](./class-SinricProDevice.md)
*   [`SettingController<SinricProThermostat>`](./capability-SettingController.md)
*   [`PushNotification<SinricProThermostat>`](./capability-PushNotification.md)
*   [`PowerStateController<SinricProThermostat>`](./capability-PowerStateController.md)
*   [`ThermostatController<SinricProThermostat>`](./capability-ThermostatController.md)
*   [`TemperatureSensor<SinricProThermostat>`](./capability-TemperatureSensor.md)

## Constructor

### `SinricProThermostat(const String &deviceId)`
*   **Purpose**: Constructs a new `SinricProThermostat` object.
*   **Parameters**:
    *   `deviceId`: A `const String&` containing the unique identifier for this thermostat device.
*   **Return Type**: None
*   **Initialization**: Initializes the base `SinricProDevice` with the provided `deviceId` and sets its product type to `"THERMOSTAT"`.

## Capabilities
By inheriting from the following controllers, `SinricProThermostat` gains their respective functionalities:

*   **ThermostatController**: Enables setting and adjusting target temperatures, and changing thermostat modes. It also allows sending events to report these states. See [`ThermostatController` documentation](./capability-ThermostatController.md) for details.

*   **TemperatureSensor**: Enables the device to send `currentTemperature` events to report the actual ambient temperature. See [`TemperatureSensor` documentation](./capability-TemperatureSensor.md) for details.

*   **PowerStateController**: Enables the thermostat to respond to `setPowerState` commands (on/off) and send `setPowerState` events to report its current status. See [`PowerStateController` documentation](./capability-PowerStateController.md) for details.

*   **SettingController**: Allows the thermostat to receive and process generic `setSetting` commands for device-specific configurations. See [`SettingController` documentation](./capability-SettingController.md) for details.

*   **PushNotification**: Provides the ability for the thermostat to send push notifications to the SinricPro App (e.g., for temperature alerts or mode changes). See [`PushNotification` documentation](./capability-PushNotification.md) for details.

## Usage Example
```cpp
#include <SinricPro.h>
#include <SinricProThermostat.h>

#define APP_KEY    "YOUR_APP_KEY"
#define APP_SECRET "YOUR_APP_SECRET"
#define THERMOSTAT_ID  "YOUR_THERMOSTAT_DEVICE_ID"

// Callback for thermostat mode changes
bool onThermostatMode(const String &deviceId, String &mode) {
  Serial.printf("Thermostat %s set mode to %s\r\n", deviceId.c_str(), mode.c_str());
  // Your code to set the physical thermostat mode
  return true;
}

// Callback for target temperature changes
bool onTargetTemperature(const String &deviceId, float &temperature) {
  Serial.printf("Thermostat %s set target temperature to %.1f°C\r\n", deviceId.c_str(), temperature);
  // Your code to set the physical target temperature
  return true;
}

void setup() {
  Serial.begin(115200);
  SinricPro.begin(APP_KEY, APP_SECRET);

  // Get a reference to your thermostat device
  SinricProThermostat &myThermostat = SinricPro[THERMOSTAT_ID];

  // Set callbacks
  myThermostat.onThermostatMode(onThermostatMode);
  myThermostat.onTargetTemperature(onTargetTemperature);

  // You can also set callbacks for other capabilities
}

void loop() {
  SinricPro.handle();

  // Example: Send current ambient temperature periodically
  static unsigned long lastTempReport = 0;
  if (millis() - lastTempReport > 60 * 1000) { // Every 1 minute
    float currentAmbientTemp = 23.0; // Read from your sensor
    myThermostat.sendTargetTemperatureEvent(currentAmbientTemp); // Note: This sends actual temp, not target
    Serial.printf("Sent current ambient temperature: %.1f°C\r\n", currentAmbientTemp);
    lastTempReport = millis();
  }
}
```

## Dependencies
*   `SinricProDevice.h`
*   `Capabilities/SettingController.h`
*   `Capabilities/PushNotification.h`
*   `Capabilities/PowerStateController.h`
*   `Capabilities/ThermostatController.h`
*   `Capabilities/TemperatureSensor.h`

## Tags
`SinricPro`, `SDK`, `Device`, `Thermostat`, `Smart Home`, `Temperature Control`, `Mode Control`, `PowerState`, `Settings`, `PushNotification`, `ESP8266`, `ESP32`
