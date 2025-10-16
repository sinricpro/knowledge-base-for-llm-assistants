# SinricProLight

## Overview
The `SinricProLight` class represents a smart light device within the SinricPro ecosystem. It is designed to provide comprehensive control over lighting, including basic on/off functionality, adjustable brightness, RGB color control, and color temperature adjustments. This class aggregates functionalities from various controller capabilities.

## Inheritance
`SinricProLight` inherits from:
*   [`SinricProDevice`](./class-SinricProDevice.md)
*   [`SettingController<SinricProLight>`](./capability-SettingController.md)
*   [`PushNotification<SinricProLight>`](./capability-PushNotification.md)
*   [`PowerStateController<SinricProLight>`](./capability-PowerStateController.md)
*   [`BrightnessController<SinricProLight>`](./capability-BrightnessController.md)
*   [`ColorController<SinricProLight>`](./capability-ColorController.md)
*   [`ColorTemperatureController<SinricProLight>`](./capability-ColorTemperatureController.md)

## Constructor

### `SinricProLight(const String &deviceId)`
*   **Purpose**: Constructs a new `SinricProLight` object.
*   **Parameters**:
    *   `deviceId`: A `const String&` containing the unique identifier for this light device.
*   **Return Type**: None
*   **Initialization**: Initializes the base `SinricProDevice` with the provided `deviceId` and sets its product type to `"LIGHT"`.

## Capabilities
By inheriting from the following controllers, `SinricProLight` gains their respective functionalities:

*   **PowerStateController**: Enables the light to respond to `setPowerState` commands (on/off) and send `setPowerState` events to report its current status. See [`PowerStateController` documentation](./capability-PowerStateController.md) for details.

*   **BrightnessController**: Allows setting an absolute brightness level and adjusting it relatively. It also enables sending events to report the current brightness. See [`BrightnessController` documentation](./capability-BrightnessController.md) for details.

*   **ColorController**: Provides control over the light's RGB color, allowing setting specific colors and sending events to report the current color. See [`ColorController` documentation](./capability-ColorController.md) for details.

*   **ColorTemperatureController**: Enables setting an absolute color temperature and adjusting it relatively. It also allows sending events to report the current color temperature. See [`ColorTemperatureController` documentation](./capability-ColorTemperatureController.md) for details.

*   **SettingController**: Allows the light to receive and process generic `setSetting` commands for device-specific configurations. See [`SettingController` documentation](./capability-SettingController.md) for details.

*   **PushNotification**: Provides the ability for the light to send push notifications to the SinricPro App. See [`PushNotification` documentation](./capability-PushNotification.md) for details.

## Usage Example
```cpp
#include <SinricPro.h>
#include <SinricProLight.h>

#define APP_KEY    "YOUR_APP_KEY"
#define APP_SECRET "YOUR_APP_SECRET"
#define LIGHT_ID  "YOUR_LIGHT_DEVICE_ID"

// Callback for power state changes
bool onPowerState(const String &deviceId, bool &state) {
  Serial.printf("Light %s turned %s\r\n", deviceId.c_str(), state ? "ON" : "OFF");
  // Your code to control the physical light power
  return true;
}

// Callback for brightness changes
bool onBrightness(const String &deviceId, int &brightness) {
  Serial.printf("Light %s set brightness to %d\r\n", deviceId.c_str(), brightness);
  // Your code to set the light brightness
  return true;
}

// Callback for color changes
bool onColor(const String &deviceId, byte &r, byte &g, byte &b) {
  Serial.printf("Light %s set color to R:%d G:%d B:%d\r\n", deviceId.c_str(), r, g, b);
  // Your code to set the light color
  return true;
}

// Callback for color temperature changes
bool onColorTemperature(const String &deviceId, int &colorTemperature) {
  Serial.printf("Light %s set color temperature to %dK\r\n", deviceId.c_str(), colorTemperature);
  // Your code to set the light color temperature
  return true;
}

void setup() {
  Serial.begin(115200);
  SinricPro.begin(APP_KEY, APP_SECRET);

  // Get a reference to your light device
  SinricProLight &myLight = SinricPro[LIGHT_ID];

  // Set callbacks
  myLight.onPowerState(onPowerState);
  myLight.onBrightness(onBrightness);
  myLight.onColor(onColor);
  myLight.onColorTemperature(onColorTemperature);

  // You can also set callbacks for settings or push notifications if needed
}

void loop() {
  SinricPro.handle();

  // Example: Send current light state if changed locally
  // myLight.sendPowerStateEvent(true); // Light is ON
  // myLight.sendBrightnessEvent(75); // Light is 75% bright
}
```

## Dependencies
*   `SinricProDevice.h`
*   `Capabilities/SettingController.h`
*   `Capabilities/PushNotification.h`
*   `Capabilities/PowerStateController.h`
*   `Capabilities/BrightnessController.h`
*   `Capabilities/ColorController.h`
*   `Capabilities/ColorTemperatureController.h`

## Tags
`SinricPro`, `SDK`, `Device`, `Light`, `Smart Home`, `PowerState`, `Brightness`, `Color`, `Color Temperature`, `Settings`, `PushNotification`, `ESP8266`, `ESP32`
