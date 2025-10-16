# SinricProTV

## Overview
The `SinricProTV` class represents a smart television device within the SinricPro ecosystem. It provides extensive control over TV functions, including power, volume, mute, media playback, input selection, and channel management. This class aggregates functionalities from various controller capabilities.

## Inheritance
`SinricProTV` inherits from:
*   [`SinricProDevice`](./class-SinricProDevice.md)
*   [`SettingController<SinricProTV>`](./capability-SettingController.md)
*   [`PushNotification<SinricProTV>`](./capability-PushNotification.md)
*   [`PowerStateController<SinricProTV>`](./capability-PowerStateController.md)
*   [`VolumeController<SinricProTV>`](./capability-VolumeController.md)
*   [`MuteController<SinricProTV>`](./capability-MuteController.md)
*   [`MediaController<SinricProTV>`](./capability-MediaController.md)
*   [`InputController<SinricProTV>`](./capability-InputController.md)
*   [`ChannelController<SinricProTV>`](./capability-ChannelController.md)

## Constructor

### `SinricProTV(const String &deviceId)`
*   **Purpose**: Constructs a new `SinricProTV` object.
*   **Parameters**:
    *   `deviceId`: A `const String&` containing the unique identifier for this TV device.
*   **Return Type**: None
*   **Initialization**: Initializes the base `SinricProDevice` with the provided `deviceId` and sets its product type to `"TV"`.

## Capabilities
By inheriting from the following controllers, `SinricProTV` gains their respective functionalities:

*   **PowerStateController**: Enables the TV to respond to `setPowerState` commands (on/off) and send `setPowerState` events to report its current status. See [`PowerStateController` documentation](./capability-PowerStateController.md) for details.

*   **VolumeController**: Provides control over the TV's volume level (absolute and relative adjustments), and enables sending events to report the current volume. See [`VolumeController` documentation](./capability-VolumeController.md) for details.

*   **MuteController**: Allows muting and unmuting the TV, and sending events to report the mute status. See [`MuteController` documentation](./capability-MuteController.md) for details.

*   **MediaController**: Enables control over media playback actions such as Play, Pause, Stop, Next, Previous, FastForward, Rewind, and StartOver. See [`MediaController` documentation](./capability-MediaController.md) for details.

*   **InputController**: Allows selecting different audio/video input sources for the TV. See [`InputController` documentation](./capability-InputController.md) for details.

*   **ChannelController**: Provides control over TV channels, including changing by name or number, and skipping channels. See [`ChannelController` documentation](./capability-ChannelController.md) for details.

*   **SettingController**: Allows the TV to receive and process generic `setSetting` commands for device-specific configurations. See [`SettingController` documentation](./capability-SettingController.md) for details.

*   **PushNotification**: Provides the ability for the TV to send push notifications to the SinricPro App. See [`PushNotification` documentation](./capability-PushNotification.md) for details.

## Usage Example
```cpp
#include <SinricPro.h>
#include <SinricProTV.h>

#define APP_KEY    "YOUR_APP_KEY"
#define APP_SECRET "YOUR_APP_SECRET"
#define TV_ID  "YOUR_TV_DEVICE_ID"

// Callback for power state changes
bool onPowerState(const String &deviceId, bool &state) {
  Serial.printf("TV %s turned %s\r\n", deviceId.c_str(), state ? "ON" : "OFF");
  // Your code to control the physical TV power
  return true;
}

// Callback for volume changes
bool onSetVolume(const String &deviceId, int &volume) {
  Serial.printf("TV %s set volume to %d\r\n", deviceId.c_str(), volume);
  // Your code to set the TV volume
  return true;
}

// Callback for channel changes by name
bool onChangeChannel(const String &deviceId, String &channelName) {
  Serial.printf("TV %s requested to change to channel: %s\r\n", deviceId.c_str(), channelName.c_str());
  // Your code to change the TV channel by name
  return true;
}

void setup() {
  Serial.begin(115200);
  SinricPro.begin(APP_KEY, APP_SECRET);

  // Get a reference to your TV device
  SinricProTV &myTV = SinricPro[TV_ID];

  // Set callbacks
  myTV.onPowerState(onPowerState);
  myTV.onSetVolume(onSetVolume);
  myTV.onChangeChannel(onChangeChannel);

  // You can set other callbacks as needed (mute, media, input, settings, push notifications)
}

void loop() {
  SinricPro.handle();

  // Example: Send current volume if changed locally
  // myTV.sendVolumeEvent(45);
}
```

## Dependencies
*   `SinricProDevice.h`
*   `Capabilities/SettingController.h`
*   `Capabilities/PushNotification.h`
*   `Capabilities/PowerStateController.h`
*   `Capabilities/VolumeController.h`
*   `Capabilities/MuteController.h`
*   `Capabilities/MediaController.h`
*   `Capabilities/InputController.h`
*   `Capabilities/ChannelController.h`

## Tags
`SinricPro`, `SDK`, `Device`, `TV`, `Smart Home`, `PowerState`, `Volume`, `Mute`, `Media Control`, `Input`, `Channel`, `Settings`, `PushNotification`, `ESP8266`, `ESP32`
