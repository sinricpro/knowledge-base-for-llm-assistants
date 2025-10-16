# MuteController

## Overview
The `MuteController` is a template class that provides functionality for SinricPro devices to control and report their mute state. This capability is commonly used for audio/video devices like smart speakers, TVs, or soundbars, allowing users to mute or unmute the device remotely.

## Template Parameters
*   `T`: The derived class that incorporates `MuteController`. This allows the controller to interact with the derived device's properties and methods (e.g., `deviceId`, `prepareEvent`, `sendEvent`).

## Callback Handlers (Typedefs)

### `MuteCallback`
*   **Purpose**: Defines the signature for the callback function that is invoked when the device receives a `setMute` request from the SinricPro server.
*   **Signature**: `std::function<bool(const String &deviceId, bool &mute)>`
*   **Parameters**:
    *   `deviceId`: A `String` containing the unique ID of the device.
    *   `mute`: A `bool` reference. On input, `true` indicates a request to mute the device, and `false` indicates a request to unmute. The callback should update this parameter to reflect the actual mute state of the device after processing the request (`true` for muted, `false` for unmuted).
*   **Return Type**: `bool`
    *   `true`: The request was handled successfully.
    *   `false`: The request could not be handled properly.
*   **Usage Example**:
    ```cpp
    bool onMute(const String &deviceId, bool &mute) {
      Serial.printf("Device %s requested to be %s\r\n", deviceId.c_str(), mute ? "muted" : "unmuted");
      // Your code to mute/unmute the device
      return true;
    }
    // In setup:
    // mySpeaker.onMute(onMute);
    ```

## Public Methods

### `MuteController()`
*   **Purpose**: Constructor for the `MuteController`. It initializes an `EventLimiter` and registers a request handler with the derived device class to process mute-related requests.
*   **Parameters**: None
*   **Return Type**: None

### `onMute(MuteCallback cb)`
*   **Purpose**: Sets the callback function that will be invoked when the SinricPro server sends a `setMute` command to the device.
*   **Parameters**:
    *   `cb`: A `MuteCallback` function pointer or lambda that will handle the mute state change request.
*   **Return Type**: `void`

### `sendMuteEvent(bool mute, String cause = FSTR_SINRICPRO_PHYSICAL_INTERACTION)`
*   **Purpose**: Sends a `setMute` event to the SinricPro server, informing it about the device's current mute state. This is typically called when the device's mute state changes locally.
*   **Parameters**:
    *   `mute`: A `bool` indicating the current mute state (`true` for muted, `false` for unmuted).
    *   `cause`: (Optional) A `String` describing the reason for the event (default is `"PHYSICAL_INTERACTION"`).
*   **Return Type**: `bool`
    *   `true`: The event was sent successfully.
    *   `false`: The event was not sent, possibly due to event rate limiting.
*   **Usage Example**:
    ```cpp
    // After muting the device locally
    // mySpeaker.sendMuteEvent(true);
    ```

## Protected Methods

### `handleMuteController(SinricProRequest &request)`
*   **Purpose**: Internal method used by the `MuteController` to process incoming `SinricProRequest` objects. It checks if the request is a `setMute` action and, if so, invokes the registered `MuteCallback`.
*   **Parameters**:
    *   `request`: A reference to the `SinricProRequest` object received from the server.
*   **Return Type**: `bool` (Indicates if the request was handled by this controller.)

## Dependencies
*   `../SinricProRequest.h`
*   `../EventLimiter.h`
*   `../SinricProStrings.h`
*   `../SinricProNamespace.h`

## Tags
`SinricPro`, `SDK`, `Capability`, `Mute`, `Audio`, `Controller`, `Smart Home`, `ESP8266`, `ESP32`
