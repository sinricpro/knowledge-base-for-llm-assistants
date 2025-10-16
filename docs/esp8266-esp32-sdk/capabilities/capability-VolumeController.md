# VolumeController

## Overview
The `VolumeController` is a template class that provides functionality for SinricPro devices to control and report their volume level. This is a common capability for audio devices such as smart speakers, soundbars, or TVs, allowing users to set an absolute volume or adjust it relatively.

## Template Parameters
*   `T`: The derived class that incorporates `VolumeController`. This allows the controller to interact with the derived device's properties and methods (e.g., `deviceId`, `prepareEvent`, `sendEvent`).

## Callback Handlers (Typedefs)

### `SetVolumeCallback`
*   **Purpose**: Defines the signature for the callback function that is invoked when the device receives a `setVolume` request from the SinricPro server.
*   **Signature**: `std::function<bool(const String &deviceId, int &volume)>`
*   **Parameters**:
    *   `deviceId`: A `String` containing the unique ID of the device.
    *   `volume`: An `int` reference. On input, it represents the requested absolute volume level. The callback should update this parameter to reflect the actual volume level set by the device.
*   **Return Type**: `bool`
    *   `true`: The request was handled successfully.
    *   `false`: The request could not be handled properly.
*   **Usage Example**:
    ```cpp
    bool onSetVolume(const String &deviceId, int &volume) {
      Serial.printf("Device %s set volume to %d\r\n", deviceId.c_str(), volume);
      // Your code to set the device volume
      return true;
    }
    // In setup:
    // mySpeaker.onSetVolume(onSetVolume);
    ```

### `AdjustVolumeCallback`
*   **Purpose**: Defines the signature for the callback function that is invoked when the device receives an `adjustVolume` request from the SinricPro server.
*   **Signature**: `std::function<bool(const String &deviceId, int &volumeDelta, bool volumeDefault)>`
*   **Parameters**:
    *   `deviceId`: A `String` containing the unique ID of the device.
    *   `volumeDelta`: An `int` reference. On input, it represents the relative change in volume (e.g., +5, -10). The callback should update this parameter to reflect the new absolute volume level after the adjustment.
    *   `volumeDefault`: A `bool` indicating whether the adjustment was a default step (`true`) or a specific amount (`false`) provided by the user.
*   **Return Type**: `bool`
    *   `true`: The request was handled successfully.
    *   `false`: The request could not be handled properly.
*   **Usage Example**:
    ```cpp
    bool onAdjustVolume(const String &deviceId, int &volumeDelta, bool volumeDefault) {
      Serial.printf("Device %s adjusted volume by %d (default: %s)\r\n", deviceId.c_str(), volumeDelta, volumeDefault ? "true" : "false");
      // Your code to adjust the device volume
      return true;
    }
    // In setup:
    // mySpeaker.onAdjustVolume(onAdjustVolume);
    ```

## Public Methods

### `VolumeController()`
*   **Purpose**: Constructor for the `VolumeController`. It initializes an `EventLimiter` and registers a request handler with the derived device class to process volume-related requests.
*   **Parameters**: None
*   **Return Type**: None

### `onSetVolume(SetVolumeCallback cb)`
*   **Purpose**: Sets the callback function that will be invoked when the SinricPro server sends a `setVolume` command to the device.
*   **Parameters**:
    *   `cb`: A `SetVolumeCallback` function pointer or lambda that will handle the absolute volume setting request.
*   **Return Type**: `void`

### `onAdjustVolume(AdjustVolumeCallback cb)`
*   **Purpose**: Sets the callback function that will be invoked when the SinricPro server sends an `adjustVolume` command to the device.
*   **Parameters**:
    *   `cb`: An `AdjustVolumeCallback` function pointer or lambda that will handle the relative volume adjustment request.
*   **Return Type**: `void`

### `sendVolumeEvent(int volume, String cause = FSTR_SINRICPRO_PHYSICAL_INTERACTION)`
*   **Purpose**: Sends a `setVolume` event to the SinricPro server, informing it about the device's current absolute volume level. This is typically called when the device's volume changes locally.
*   **Parameters**:
    *   `volume`: An `int` indicating the current absolute volume level.
    *   `cause`: (Optional) A `String` describing the reason for the event (default is `"PHYSICAL_INTERACTION"`).
*   **Return Type**: `bool`
    *   `true`: The event was sent successfully.
    *   `false`: The event was not sent, possibly due to event rate limiting.
*   **Usage Example**:
    ```cpp
    // After setting volume locally
    // mySpeaker.sendVolumeEvent(50);
    ```

## Protected Methods

### `handleVolumeController(SinricProRequest &request)`
*   **Purpose**: Internal method used by the `VolumeController` to process incoming `SinricProRequest` objects. It checks if the request is a `setVolume` or `adjustVolume` action and, if so, invokes the corresponding registered callback.
*   **Parameters**:
    *   `request`: A reference to the `SinricProRequest` object received from the server.
*   **Return Type**: `bool` (Indicates if the request was handled by this controller.)

## Dependencies
*   `../SinricProRequest.h`
*   `../EventLimiter.h`
*   `../SinricProStrings.h`
*   `../SinricProNamespace.h`

## Tags
`SinricPro`, `SDK`, `Capability`, `Volume`, `Audio`, `Controller`, `Smart Home`, `Speaker`, `TV`, `ESP8266`, `ESP32`
