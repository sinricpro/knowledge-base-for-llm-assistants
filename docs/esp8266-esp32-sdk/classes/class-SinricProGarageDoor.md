# SinricProGarageDoor

## Overview
The `SinricProGarageDoor` class represents a smart garage door device within the SinricPro ecosystem. It is designed to control the opening and closing of a garage door and can also manage device settings and send push notifications. This class aggregates functionalities from various controller capabilities.

## Inheritance
`SinricProGarageDoor` inherits from:
*   [`SinricProDevice`](./class-SinricProDevice.md)
*   [`SettingController<SinricProGarageDoor>`](./capability-SettingController.md)
*   [`PushNotification<SinricProGarageDoor>`](./capability-PushNotification.md)
*   [`DoorController<SinricProGarageDoor>`](./capability-DoorController.md)

## Constructor

### `SinricProGarageDoor(const String &deviceId)`
*   **Purpose**: Constructs a new `SinricProGarageDoor` object.
*   **Parameters**:
    *   `deviceId`: A `const String&` containing the unique identifier for this garage door device.
*   **Return Type**: None
*   **Initialization**: Initializes the base `SinricProDevice` with the provided `deviceId` and sets its product type to `"GARAGE_DOOR"`.

## Capabilities
By inheriting from the following controllers, `SinricProGarageDoor` gains their respective functionalities:

*   **DoorController**: Enables the garage door to respond to `setMode` commands (open/close) and send `setMode` events to report its current status. See [`DoorController` documentation](./capability-DoorController.md) for details.

*   **SettingController**: Allows the garage door to receive and process generic `setSetting` commands for device-specific configurations. See [`SettingController` documentation](./capability-SettingController.md) for details.

*   **PushNotification**: Provides the ability for the garage door to send push notifications to the SinricPro App (e.g., for status changes or security alerts). See [`PushNotification` documentation](./capability-PushNotification.md) for details.

## Usage Example
```cpp
#include <SinricPro.h>
#include <SinricProGarageDoor.h>

#define APP_KEY    "YOUR_APP_KEY"
#define APP_SECRET "YOUR_APP_SECRET"
#define GARAGE_DOOR_ID  "YOUR_GARAGE_DOOR_DEVICE_ID"

// Callback for door state changes
bool onDoorState(const String &deviceId, bool &doorState) {
  Serial.printf("Garage Door %s requested to %s\r\n", deviceId.c_str(), doorState ? "close" : "open");
  // Your code to control the physical garage door (e.g., activate relay)
  return true;
}

void setup() {
  Serial.begin(115200);
  SinricPro.begin(APP_KEY, APP_SECRET);

  // Get a reference to your garage door device
  SinricProGarageDoor &myGarageDoor = SinricPro[GARAGE_DOOR_ID];

  // Set callbacks
  myGarageDoor.onDoorState(onDoorState);

  // You can also set callbacks for settings or push notifications if needed
}

void loop() {
  SinricPro.handle();

  // Example: Send current door state if changed locally
  // myGarageDoor.sendDoorStateEvent(true); // Door is closed
}
```

## Dependencies
*   `SinricProDevice.h`
*   `Capabilities/SettingController.h`
*   `Capabilities/PushNotification.h`
*   `Capabilities/DoorController.h`

## Tags
`SinricPro`, `SDK`, `Device`, `Garage Door`, `Smart Home`, `Door Control`, `Settings`, `PushNotification`, `ESP8266`, `ESP32`
