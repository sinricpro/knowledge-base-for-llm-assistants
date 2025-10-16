# EqualizerController

## Overview
The `EqualizerController` is a template class that provides functionality for SinricPro devices to control and report equalizer band levels. This is typically used for audio devices, allowing control over specific frequency ranges like Bass, Midrange, and Treble. It supports setting absolute levels, adjusting levels relatively, and resetting bands to their default state.

## Template Parameters
*   `T`: The derived class that incorporates `EqualizerController`. This allows the controller to interact with the derived device's properties and methods (e.g., `deviceId`, `prepareEvent`, `sendEvent`). 

## Callback Handlers (Typedefs)

### `SetBandsCallback`
*   **Purpose**: Defines the signature for the callback function that is invoked when the device receives a `setBands` request from the SinricPro server.
*   **Signature**: `std::function<bool(const String &deviceId, const String &bands, int &level)>`
*   **Parameters**:
    *   `deviceId`: A `String` containing the unique ID of the device.
    *   `bands`: A `String` indicating the specific equalizer band to set (e.g., `"BASS"`, `"MIDRANGE"`, `"TREBBLE"`).
    *   `level`: An `int` reference. On input, it represents the requested absolute level for the band. The callback should update this parameter to reflect the actual level set by the device.
*   **Return Type**: `bool`
    *   `true`: The request was handled successfully.
    *   `false`: The request could not be handled properly.
*   **Usage Example**:
    ```cpp
    bool onSetBands(const String &deviceId, const String &bands, int &level) {
      Serial.printf("Device %s set %s band to %d\r\n", deviceId.c_str(), bands.c_str(), level);
      // Your code to set the equalizer band level
      return true;
    }
    // In setup:
    // myAudioDevice.onSetBands(onSetBands);
    ```

### `AdjustBandsCallback`
*   **Purpose**: Defines the signature for the callback function that is invoked when the device receives an `adjustBands` request from the SinricPro server.
*   **Signature**: `std::function<bool(const String &deviceId, const String &bands, int &levelDelta)>`
*   **Parameters**:
    *   `deviceId`: A `String` containing the unique ID of the device.
    *   `bands`: A `String` indicating the specific equalizer band to adjust.
    *   `levelDelta`: An `int` reference. On input, it represents the relative change in level (e.g., +5, -10). The callback should update this parameter to reflect the new absolute level after the adjustment.
*   **Return Type**: `bool`
    *   `true`: The request was handled successfully.
    *   `false`: The request could not be handled properly.
*   **Usage Example**:
    ```cpp
    bool onAdjustBands(const String &deviceId, const String &bands, int &levelDelta) {
      Serial.printf("Device %s adjusted %s band by %d\r\n", deviceId.c_str(), bands.c_str(), levelDelta);
      // Your code to adjust the equalizer band level
      return true;
    }
    // In setup:
    // myAudioDevice.onAdjustBands(onAdjustBands);
    ```

### `ResetBandsCallback`
*   **Purpose**: Defines the signature for the callback function that is invoked when the device receives a `resetBands` request from the SinricPro server.
*   **Signature**: `std::function<bool(const String &deviceId, const String &bands, int &level)>`
*   **Parameters**:
    *   `deviceId`: A `String` containing the unique ID of the device.
    *   `bands`: A `String` indicating the specific equalizer band to reset.
    *   `level`: An `int` reference. On input, it's typically `0` (indicating a reset). The callback should update this parameter to reflect the new absolute level after the reset (usually 0).
*   **Return Type**: `bool`
    *   `true`: The request was handled successfully.
    *   `false`: The request could not be handled properly.
*   **Usage Example**:
    ```cpp
    bool onResetBands(const String &deviceId, const String &bands, int &level) {
      Serial.printf("Device %s reset %s band\r\n", deviceId.c_str(), bands.c_str());
      // Your code to reset the equalizer band level
      return true;
    }
    // In setup:
    // myAudioDevice.onResetBands(onResetBands);
    ```

## Public Methods

### `EqualizerController()`
*   **Purpose**: Constructor for the `EqualizerController`. It initializes an `EventLimiter` and registers a request handler with the derived device class to process equalizer-related requests.
*   **Parameters**: None
*   **Return Type**: None

### `onSetBands(SetBandsCallback cb)`
*   **Purpose**: Sets the callback function that will be invoked when the SinricPro server sends a `setBands` command to the device.
*   **Parameters**:
    *   `cb`: A `SetBandsCallback` function pointer or lambda that will handle the absolute band level setting request.
*   **Return Type**: `void`

### `onAdjustBands(AdjustBandsCallback cb)`
*   **Purpose**: Sets the callback function that will be invoked when the SinricPro server sends an `adjustBands` command to the device.
*   **Parameters**:
    *   `cb`: An `AdjustBandsCallback` function pointer or lambda that will handle the relative band level adjustment request.
*   **Return Type**: `void`

### `onResetBands(ResetBandsCallback cb)`
*   **Purpose**: Sets the callback function that will be invoked when the SinricPro server sends a `resetBands` command to the device.
*   **Parameters**:
    *   `cb`: A `ResetBandsCallback` function pointer or lambda that will handle the band reset request.
*   **Return Type**: `void`

### `sendBandsEvent(String bands, int level, String cause = "PHYSICAL_INTERACTION")`
*   **Purpose**: Sends a `setBands` event to the SinricPro server, informing it about the device's current equalizer band level. This is typically called when the device's band level changes locally.
*   **Parameters**:
    *   `bands`: A `String` indicating the specific equalizer band that changed (e.g., `"BASS"`).
    *   `level`: An `int` indicating the current absolute level of the band.
    *   `cause`: (Optional) A `String` describing the reason for the event (default is `"PHYSICAL_INTERACTION"`).
*   **Return Type**: `bool`
    *   `true`: The event was sent successfully.
    *   `false`: The event was not sent, possibly due to event rate limiting.
*   **Usage Example**:
    ```cpp
    // After setting bass level locally
    // myAudioDevice.sendBandsEvent("BASS", 5);
    ```

## Protected Methods

### `handleEqualizerController(SinricProRequest &request)`
*   **Purpose**: Internal method used by the `EqualizerController` to process incoming `SinricProRequest` objects. It checks if the request is a `setBands`, `adjustBands`, or `resetBands` action and, if so, invokes the corresponding registered callback.
*   **Parameters**:
    *   `request`: A reference to the `SinricProRequest` object received from the server.
*   **Return Type**: `bool` (Indicates if the request was handled by this controller.)

## Dependencies
*   `../SinricProRequest.h`
*   `../EventLimiter.h`
*   `../SinricProStrings.h`
*   `../SinricProNamespace.h`

## Tags
`SinricPro`, `SDK`, `Capability`, `Equalizer`, `Audio`, `Controller`, `Smart Home`, `ESP8266`, `ESP32`
