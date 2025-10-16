# SinricProSpeaker

## Overview
The `SinricProSpeaker` class represents a smart speaker device within the SinricPro ecosystem. It provides extensive control over audio playback, including volume, mute, media transport controls, input selection, equalizer settings, and various operational modes. This class aggregates functionalities from a wide range of controller capabilities.

## Inheritance
`SinricProSpeaker` inherits from:
*   [`SinricProDevice`](./class-SinricProDevice.md)
*   [`SettingController<SinricProSpeaker>`](./capability-SettingController.md)
*   [`PushNotification<SinricProSpeaker>`](./capability-PushNotification.md)
*   [`PowerStateController<SinricProSpeaker>`](./capability-PowerStateController.md)
*   [`MuteController<SinricProSpeaker>`](./capability-MuteController.md)
*   [`VolumeController<SinricProSpeaker>`](./capability-VolumeController.md)
*   [`MediaController<SinricProSpeaker>`](./capability-MediaController.md)
*   [`InputController<SinricProSpeaker>`](./capability-InputController.md)
*   [`EqualizerController<SinricProSpeaker>`](./capability-EqualizerController.md)
*   [`ModeController<SinricProSpeaker>`](./capability-ModeController.md)

## Constructor

### `SinricProSpeaker(const String &deviceId)`
*   **Purpose**: Constructs a new `SinricProSpeaker` object.
*   **Parameters**:
    *   `deviceId`: A `const String&` containing the unique identifier for this speaker device.
*   **Return Type**: None
*   **Initialization**: Initializes the base `SinricProDevice` with the provided `deviceId` and sets its product type to `"SPEAKER"`.

## Capabilities
By inheriting from the following controllers, `SinricProSpeaker` gains their respective functionalities:

*   **PowerStateController**: Enables the speaker to respond to `setPowerState` commands (on/off) and send `setPowerState` events to report its current status. See [`PowerStateController` documentation](./capability-PowerStateController.md) for details.

*   **MuteController**: Allows muting and unmuting the speaker, and sending events to report the mute status. See [`MuteController` documentation](./capability-MuteController.md) for details.

*   **VolumeController**: Provides control over the speaker's volume level (absolute and relative adjustments), and enables sending events to report the current volume. See [`VolumeController` documentation](./capability-VolumeController.md) for details.

*   **MediaController**: Enables control over media playback actions such as Play, Pause, Stop, Next, Previous, FastForward, Rewind, and StartOver. See [`MediaController` documentation](./capability-MediaController.md) for details.

*   **InputController**: Allows selecting different audio input sources for the speaker. See [`InputController` documentation](./capability-InputController.md) for details.

*   **EqualizerController**: Provides control over equalizer bands (e.g., Bass, Midrange, Treble) for audio customization. See [`EqualizerController` documentation](./capability-EqualizerController.md) for details.

*   **ModeController**: Enables setting various operational modes for the speaker (e.g., `"MOVIE"`, `"MUSIC"`). See [`ModeController` documentation](./capability-ModeController.md) for details.

*   **SettingController**: Allows the speaker to receive and process generic `setSetting` commands for device-specific configurations. See [`SettingController` documentation](./capability-SettingController.md) for details.

*   **PushNotification**: Provides the ability for the speaker to send push notifications to the SinricPro App. See [`PushNotification` documentation](./capability-PushNotification.md) for details.

## Usage Example
```cpp
#include <SinricPro.h>
#include <SinricProSpeaker.h>

#define APP_KEY    "YOUR_APP_KEY"
#define APP_SECRET "YOUR_APP_SECRET"
#define SPEAKER_ID  "YOUR_SPEAKER_DEVICE_ID"

// Callback for power state changes
bool onPowerState(const String &deviceId, bool &state) {
  Serial.printf("Speaker %s turned %s\r\n", deviceId.c_str(), state ? "ON" : "OFF");
  return true;
}

// Callback for volume changes
bool onSetVolume(const String &deviceId, int &volume) {
  Serial.printf("Speaker %s set volume to %d\r\n", deviceId.c_str(), volume);
  return true;
}

// Callback for media control
bool onMediaControl(const String &deviceId, String &control) {
  Serial.printf("Speaker %s received media control: %s\r\n", deviceId.c_str(), control.c_str());
  return true;
}

void setup() {
  Serial.begin(115200);
  SinricPro.begin(APP_KEY, APP_SECRET);

  // Get a reference to your speaker device
  SinricProSpeaker &mySpeaker = SinricPro[SPEAKER_ID];

  // Set callbacks
  mySpeaker.onPowerState(onPowerState);
  mySpeaker.onSetVolume(onSetVolume);
  mySpeaker.onMediaControl(onMediaControl);

  // You can set other callbacks as needed (mute, input, equalizer, mode, settings, push notifications)
}

void loop() {
  SinricPro.handle();

  // Example: Send current volume if changed locally
  // mySpeaker.sendVolumeEvent(60);
}
```

## Dependencies
*   `SinricProDevice.h`
*   `Capabilities/SettingController.h`
*   `Capabilities/PushNotification.h`
*   `Capabilities/PowerStateController.h`
*   `Capabilities/MuteController.h`
*   `Capabilities/VolumeController.h`
*   `Capabilities/MediaController.h`
*   `Capabilities/InputController.h`
*   `Capabilities/EqualizerController.h`
*   `Capabilities/ModeController.h`

## Tags
`SinricPro`, `SDK`, `Device`, `Speaker`, `Smart Home`, `Audio`, `Volume`, `Mute`, `Media Control`, `Input`, `Equalizer`, `Mode`, `PowerState`, `Settings`, `PushNotification`, `ESP8266`, `ESP32`
