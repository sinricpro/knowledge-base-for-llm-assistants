# SinricProDoorbell

## Overview
The `SinricProDoorbell` class represents a doorbell device within the SinricPro ecosystem. It is designed to report doorbell press events and can also manage device settings, send push notifications, and respond to power state commands. This class aggregates functionalities from various controller capabilities.

## Inheritance
`SinricProDoorbell` inherits from:
*   [`SinricProDevice`](./class-SinricProDevice.md)
*   [`SettingController<SinricProDoorbell>`](./capability-SettingController.md)
*   [`PushNotification<SinricProDoorbell>`](./capability-PushNotification.md)
*   [`PowerStateController<SinricProDoorbell>`](./capability-PowerStateController.md)
*   [`Doorbell<SinricProDoorbell>`](./capability-Doorbell.md)

## Constructor

### `SinricProDoorbell(const String &deviceId)`
*   **Purpose**: Constructs a new `SinricProDoorbell` object.
*   **Parameters**:
    *   `deviceId`: A `const String&` containing the unique identifier for this doorbell device.
*   **Return Type**: None
*   **Initialization**: Initializes the base `SinricProDevice` with the provided `deviceId` and sets its product type to "CONTACT_SENSOR". (Note: While named `Doorbell`, its product type is `CONTACT_SENSOR` as per the code.)

## Capabilities
By inheriting from the following controllers, `SinricProDoorbell` gains their respective functionalities:

*   **Doorbell**: Enables the device to send `DoorbellPress` events when the doorbell button is pressed. See [`Doorbell` documentation](./capability-Doorbell.md) for details.

*   **PowerStateController**: Enables the doorbell to respond to `setPowerState` commands (on/off) and send `setPowerState` events to report its current status. See [`PowerStateController` documentation](./capability-PowerStateController.md) for details.

*   **SettingController**: Allows the doorbell to receive and process generic `setSetting` commands for device-specific configurations. See [`SettingController` documentation](./capability-SettingController.md) for details.

*   **PushNotification**: Provides the ability for the doorbell to send push notifications to the SinricPro App (e.g., for doorbell presses). See [`PushNotification` documentation](./capability-PushNotification.md) for details.

## Usage Example
```cpp
#include <SinricPro.h>
#include <SinricProDoorbell.h>

#define APP_KEY    "YOUR_APP_KEY"
#define APP_SECRET "YOUR_APP_SECRET"
#define DOORBELL_ID  "YOUR_DOORBELL_DEVICE_ID"

// Callback for power state changes (optional)
bool onPowerState(const String &deviceId, bool &state) {
  Serial.printf("Doorbell %s turned %s\r\n", deviceId.c_str(), state ? "ON" : "OFF");
  return true;
}

void setup() {
  Serial.begin(115200);
  SinricPro.begin(APP_KEY, APP_SECRET);

  // Get a reference to your doorbell device
  SinricProDoorbell &myDoorbell = SinricPro[DOORBELL_ID];

  // Set callbacks
  myDoorbell.onPowerState(onPowerState);

  // You can also set callbacks for settings or push notifications if needed
}

void loop() {
  SinricPro.handle();

  // Example: Send doorbell event when a physical button is pressed
  // if (digitalRead(DOORBELL_BUTTON_PIN) == LOW) { // Assuming active low button
  //   myDoorbell.sendDoorbellEvent();
  //   Serial.println("Doorbell pressed!");
  //   delay(1000); // Debounce
  // }
}
```

## Dependencies
*   `SinricProDevice.h`
*   `Capabilities/SettingController.h`
*   `Capabilities/PushNotification.h`
*   `Capabilities/PowerStateController.h`
*   `Capabilities/Doorbell.h`

## Tags
`SinricPro`, `SDK`, `Device`, `Doorbell`, `Contact Sensor`, `Smart Home`, `Event`, `PowerState`, `Settings`, `PushNotification`, `ESP8266`, `ESP32`
