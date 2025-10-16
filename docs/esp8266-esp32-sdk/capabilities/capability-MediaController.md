# MediaController

## Overview
The `MediaController` is a template class that provides functionality for SinricPro devices to control and report various media playback actions. This capability is essential for smart media players, TVs, or audio systems, allowing users to remotely control playback (e.g., Play, Pause, Stop) and navigate content (e.g., Next, Previous, FastForward, Rewind).

## Template Parameters
*   `T`: The derived class that incorporates `MediaController`. This allows the controller to interact with the derived device's properties and methods (e.g., `deviceId`, `prepareEvent`, `sendEvent`).

## Callback Handlers (Typedefs)

### `MediaControlCallback`
*   **Purpose**: Defines the signature for the callback function that is invoked when the device receives a `mediaControl` request from the SinricPro server.
*   **Signature**: `std::function<bool(const String &deviceId, String &control)>`
*   **Parameters**:
    *   `deviceId`: A `String` containing the unique ID of the device.
    *   `control`: A `String` reference. On input, it represents the requested media control action (e.g., `"Play"`, `"Pause"`, `"Next"`, `"FastForward"`). The callback should update this parameter to reflect the actual control action performed by the device.
*   **Return Type**: `bool`
    *   `true`: The request was handled successfully.
    *   `false`: The request could not be handled properly.
*   **Usage Example**:
    ```cpp
    bool onMediaControl(const String &deviceId, String &control) {
      Serial.printf("Device %s received media control: %s\r\n", deviceId.c_str(), control.c_str());
      // Your code to perform the media control action
      return true;
    }
    // In setup:
    // myMediaPlayer.onMediaControl(onMediaControl);
    ```

## Public Methods

### `MediaController()`
*   **Purpose**: Constructor for the `MediaController`. It initializes an `EventLimiter` and registers a request handler with the derived device class to process media control-related requests.
*   **Parameters**: None
*   **Return Type**: None

### `onMediaControl(MediaControlCallback cb)`
*   **Purpose**: Sets the callback function that will be invoked when the SinricPro server sends a `mediaControl` command to the device.
*   **Parameters**:
    *   `cb`: A `MediaControlCallback` function pointer or lambda that will handle the media control request.
*   **Return Type**: `void`

### `sendMediaControlEvent(String mediaControl, String cause = FSTR_SINRICPRO_PHYSICAL_INTERACTION)`
*   **Purpose**: Sends a `mediaControl` event to the SinricPro server, informing it about the device's current media playback state or action. This is typically called when the device's media state changes locally.
*   **Parameters**:
    *   `mediaControl`: A `String` indicating the current media control action (e.g., `"Play"`, `"Pause"`).
    *   `cause`: (Optional) A `String` describing the reason for the event (default is `"PHYSICAL_INTERACTION"`).
*   **Return Type**: `bool`
    *   `true`: The event was sent successfully.
    *   `false`: The event was not sent, possibly due to event rate limiting.
*   **Usage Example**:
    ```cpp
    // After playing media locally
    // myMediaPlayer.sendMediaControlEvent("Play");
    ```

## Protected Methods

### `handleMediaController(SinricProRequest &request)`
*   **Purpose**: Internal method used by the `MediaController` to process incoming `SinricProRequest` objects. It checks if the request is a `mediaControl` action and, if so, invokes the registered `MediaControlCallback`.
*   **Parameters**:
    *   `request`: A reference to the `SinricProRequest` object received from the server.
*   **Return Type**: `bool` (Indicates if the request was handled by this controller.)

## Dependencies
*   `../SinricProRequest.h`
*   `../EventLimiter.h`
*   `../SinricProStrings.h`
*   `../SinricProNamespace.h`

## Tags
`SinricPro`, `SDK`, `Capability`, `Media Control`, `Playback`, `Controller`, `Smart Home`, `TV`, `Media Player`, `ESP8266`, `ESP32`
