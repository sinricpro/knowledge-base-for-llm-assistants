# SinricProLock

## Overview
The `SinricProLock` class represents a smart lock device within the SinricPro ecosystem. It is designed to handle lock and unlock commands and can also manage device settings and send push notifications. This class aggregates functionalities from various controller capabilities.

## Inheritance
`SinricProLock` inherits from:
*   [`SinricProDevice`](./class-SinricProDevice.md)
*   [`SettingController<SinricProLock>`](./capability-SettingController.md)
*   [`PushNotification<SinricProLock>`](./capability-PushNotification.md)
*   [`LockController<SinricProLock>`](./capability-LockController.md)

## Constructor

### `SinricProLock(const String &deviceId)`
*   **Purpose**: Constructs a new `SinricProLock` object.
*   **Parameters**:
    *   `deviceId`: A `const String&` containing the unique identifier for this smart lock device.
*   **Return Type**: None
*   **Initialization**: Initializes the base `SinricProDevice` with the provided `deviceId` and sets its product type to `"SMARTLOCK"`.

## Capabilities
By inheriting from the following controllers, `SinricProLock` gains their respective functionalities:

*   **LockController**: Enables the smart lock to respond to `setLockState` commands (lock/unlock) and send `setLockState` events to report its current status. See [`LockController` documentation](./capability-LockController.md) for details.

*   **SettingController**: Allows the smart lock to receive and process generic `setSetting` commands for device-specific configurations. See [`SettingController` documentation](./capability-SettingController.md) for details.

*   **PushNotification**: Provides the ability for the smart lock to send push notifications to the SinricPro App (e.g., for lock/unlock events or security alerts). See [`PushNotification` documentation](./capability-PushNotification.md) for details.

## Usage Example
```cpp
#include <SinricPro.h>
#include <SinricProLock.h>

#define APP_KEY    "YOUR_APP_KEY"
#define APP_SECRET "YOUR_APP_SECRET"
#define LOCK_ID  "YOUR_SMART_LOCK_DEVICE_ID"

// Callback for lock state changes
bool onLockState(const String &deviceId, bool &lockState) {
  Serial.printf("Smart Lock %s requested to be %s\r\n", deviceId.c_str(), lockState ? "locked" : "unlocked");
  // Your code to control the physical lock mechanism
  return true;
}

void setup() {
  Serial.begin(115200);
  SinricPro.begin(APP_KEY, APP_SECRET);

  // Get a reference to your smart lock device
  SinricProLock &myLock = SinricPro[LOCK_ID];

  // Set callbacks
  myLock.onLockState(onLockState);

  // You can also set callbacks for settings or push notifications if needed
}

void loop() {
  SinricPro.handle();

  // Example: Send current lock state if changed locally
  // myLock.sendLockStateEvent(true); // Lock is locked
}
```

## Dependencies
*   `SinricProDevice.h`
*   `Capabilities/SettingController.h`
*   `Capabilities/PushNotification.h`
*   `Capabilities/LockController.h`

## Tags
`SinricPro`, `SDK`, `Device`, `Smart Lock`, `Lock`, `Security`, `Smart Home`, `LockState`, `Settings`, `PushNotification`, `ESP8266`, `ESP32`
