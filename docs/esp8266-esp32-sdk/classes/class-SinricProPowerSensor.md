# SinricProPowerSensor

## Overview
The `SinricProPowerSensor` class represents a power sensor device within the SinricPro ecosystem. It is designed to report various power usage metrics such as voltage, current, power, and watt-hours. This class aggregates functionalities from various controller capabilities.

## Inheritance
`SinricProPowerSensor` inherits from:
*   [`SinricProDevice`](./class-SinricProDevice.md)
*   [`SettingController<SinricProPowerSensor>`](./capability-SettingController.md)
*   [`PushNotification<SinricProPowerSensor>`](./capability-PushNotification.md)
*   [`PowerSensor<SinricProPowerSensor>`](./capability-PowerSensor.md)

## Constructor

### `SinricProPowerSensor(const String &deviceId)`
*   **Purpose**: Constructs a new `SinricProPowerSensor` object.
*   **Parameters**:
    *   `deviceId`: A `const String&` containing the unique identifier for this power sensor device.
*   **Return Type**: None
*   **Initialization**: Initializes the base `SinricProDevice` with the provided `deviceId` and sets its product type to `"POWER_SENSOR"`.

## Capabilities
By inheriting from the following controllers, `SinricProPowerSensor` gains their respective functionalities:

*   **PowerSensor**: Enables the device to send `powerUsage` events to report voltage, current, power, and watt-hours. See [`PowerSensor` documentation](./capability-PowerSensor.md) for details.

*   **SettingController**: Allows the sensor to receive and process generic `setSetting` commands for device-specific configurations. See [`SettingController` documentation](./capability-SettingController.md) for details.

*   **PushNotification**: Provides the ability for the sensor to send push notifications to the SinricPro App (e.g., for high power consumption alerts). See [`PushNotification` documentation](./capability-PushNotification.md) for details.

## Usage Example
```cpp
#include <SinricPro.h>
#include <SinricProPowerSensor.h>

#define APP_KEY    "YOUR_APP_KEY"
#define APP_SECRET "YOUR_APP_SECRET"
#define SENSOR_ID  "YOUR_POWER_SENSOR_DEVICE_ID"

// Callback for generic settings (optional)
bool onSetSetting(const String &deviceId, const String &settingId, String &settingValue) {
  Serial.printf("Device %s received setting %s = %s\r\n", deviceId.c_str(), settingId.c_str(), settingValue.c_str());
  return true;
}

void setup() {
  Serial.begin(115200);
  SinricPro.begin(APP_KEY, APP_SECRET);

  // Get a reference to your power sensor device
  SinricProPowerSensor &myPowerSensor = SinricPro[SENSOR_ID];

  // Set the callback for generic settings
  myPowerSensor.onSetSetting(onSetSetting);

  // You can also set callbacks for push notifications if needed
}

void loop() {
  SinricPro.handle();

  // Example: Send power usage data periodically
  static unsigned long lastReport = 0;
  if (millis() - lastReport > 10 * 1000) { // Every 10 seconds
    float voltage = 230.5; // Simulate voltage
    float current = 1.5;   // Simulate current
    float power = voltage * current; // Calculate power
    myPowerSensor.sendPowerSensorEvent(voltage, current, power);
    Serial.printf("Sent Power Event: V=%.1f, A=%.1f, W=%.1f\r\n", voltage, current, power);
    lastReport = millis();
  }
}
```

## Dependencies
*   `SinricProDevice.h`
*   `Capabilities/SettingController.h`
*   `Capabilities/PushNotification.h`
*   `Capabilities/PowerSensor.h`

## Tags
`SinricPro`, `SDK`, `Device`, `Power Sensor`, `Energy Monitoring`, `Sensor`, `Smart Home`, `ESP8266`, `ESP32`
