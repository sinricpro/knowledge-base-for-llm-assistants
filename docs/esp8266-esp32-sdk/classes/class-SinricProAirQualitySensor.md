# SinricProAirQualitySensor

## Overview
The `SinricProAirQualitySensor` class represents an air quality sensor device within the SinricPro ecosystem. It is designed to report air quality events, specifically PM1, PM2.5, and PM10 particle concentrations. This class aggregates functionalities from various controller capabilities.

## Inheritance
`SinricProAirQualitySensor` inherits from:
*   [`SinricProDevice`](./class-SinricProDevice.md)
*   [`SettingController<SinricProAirQualitySensor>`](./capability-SettingController.md)
*   [`PushNotification<SinricProAirQualitySensor>`](./capability-PushNotification.md)
*   [`AirQualitySensor<SinricProAirQualitySensor>`](./capability-AirQualitySensor.md)

## Constructor

### `SinricProAirQualitySensor(const String &deviceId)`
*   **Purpose**: Constructs a new `SinricProAirQualitySensor` object.
*   **Parameters**:
    *   `deviceId`: A `const String&` containing the unique identifier for this air quality sensor device.
*   **Return Type**: None
*   **Initialization**: Initializes the base `SinricProDevice` with the provided `deviceId` and sets its product type to "AIR_QUALITY_SENSOR".

## Capabilities
By inheriting from the following controllers, `SinricProAirQualitySensor` gains their respective functionalities:

*   **AirQualitySensor**: Enables the device to send `airQuality` events to report PM1, PM2.5, and PM10 particle concentrations. See [`AirQualitySensor` documentation](./capability-AirQualitySensor.md) for details.

*   **SettingController**: Allows the sensor to receive and process generic `setSetting` commands for device-specific configurations. See [`SettingController` documentation](./capability-SettingController.md) for details.

*   **PushNotification**: Provides the ability for the sensor to send push notifications to the SinricPro App (e.g., for high pollution alerts). See [`PushNotification` documentation](./capability-PushNotification.md) for details.

## Usage Example
```cpp
#include <SinricPro.h>
#include <SinricProAirQualitySensor.h>

#define APP_KEY    "YOUR_APP_KEY"
#define APP_SECRET "YOUR_APP_SECRET"
#define SENSOR_ID  "YOUR_AIR_QUALITY_SENSOR_DEVICE_ID"

// Callback for generic settings (optional)
bool onSetSetting(const String &deviceId, const String &settingId, String &settingValue) {
  Serial.printf("Device %s received setting %s = %s\r\n", deviceId.c_str(), settingId.c_str(), settingValue.c_str());
  return true;
}

void setup() {
  Serial.begin(115200);
  SinricPro.begin(APP_KEY, APP_SECRET);

  // Get a reference to your air quality sensor device
  SinricProAirQualitySensor &myAirSensor = SinricPro[SENSOR_ID];

  // Set the callback for generic settings
  myAirSensor.onSetSetting(onSetSetting);

  // You can also set callbacks for push notifications if needed
  // myAirSensor.onButtonPress(onButtonPress); // If using a virtual button
}

void loop() {
  SinricPro.handle();

  // Example: Send air quality data every 5 minutes
  static unsigned long lastReport = 0;
  if (millis() - lastReport > 5 * 60 * 1000) { // 5 minutes
    int pm1 = random(5, 15);   // Simulate PM1 data
    int pm2_5 = random(10, 30); // Simulate PM2.5 data
    int pm10 = random(20, 50); // Simulate PM10 data
    myAirSensor.sendAirQualityEvent(pm1, pm2_5, pm10);
    Serial.printf("Sent Air Quality Event: PM1=%d, PM2.5=%d, PM10=%d\r\n", pm1, pm2_5, pm10);
    lastReport = millis();
  }
}
```

## Dependencies
*   `SinricProDevice.h`
*   `Capabilities/SettingController.h`
*   `Capabilities/PushNotification.h`
*   `Capabilities/AirQualitySensor.h`

## Tags
`SinricPro`, `SDK`, `Device`, `Air Quality Sensor`, `Sensor`, `Smart Home`, `Environmental`, `ESP8266`, `ESP32`
