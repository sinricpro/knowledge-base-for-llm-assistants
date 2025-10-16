# BrightnessController

## Overview
The `BrightnessController` is a template class designed to add brightness control capabilities to SinricPro devices. It allows devices to respond to commands for setting an absolute brightness level and adjusting the brightness relatively. This controller also enables devices to send brightness events to the SinricPro server, keeping the server updated on the device's current brightness state.

## Template Parameters
*   `T`: The derived class that incorporates `BrightnessController`. This allows the controller to interact with the derived device's properties and methods (e.g., `deviceId`, `prepareEvent`, `sendEvent`).

## Callback Handlers (Typedefs)

### `BrightnessCallback`
*   **Purpose**: Defines the signature for the callback function that is invoked when the device receives a `setBrightness` request from the SinricPro server.
*   **Signature**: `std::function<bool(const String &deviceId, int &brightness)>`
*   **Parameters**:
    *   `deviceId`: A `String` containing the unique ID of the device.
    *   `brightness`: An `int` reference. On input, it represents the requested absolute brightness level (typically 0-100). The callback should update this parameter to reflect the actual brightness level set by the device.
*   **Return Type**: `bool`
    *   `true`: The request was handled successfully.
    *   `false`: The request could not be handled properly.
*   **Usage Example**:
    ```cpp
    bool onBrightness(const String &deviceId, int &brightness) {
      Serial.printf("Device %s set brightness to %d\r\n", deviceId.c_str(), brightness);
      // Your code to set the device brightness
      // If successful, ensure 'brightness' reflects the actual state
      return true;
    }
    // In setup:
    // myLight.onBrightness(onBrightness);
    ```

### `AdjustBrightnessCallback`
*   **Purpose**: Defines the signature for the callback function that is invoked when the device receives an `adjustBrightness` request from the SinricPro server.
*   **Signature**: `std::function<bool(const String &deviceId, int &brightnessDelta)>`
*   **Parameters**:
    *   `deviceId`: A `String` containing the unique ID of the device.
    *   `brightnessDelta`: An `int` reference. On input, it represents the requested relative change in brightness (e.g., +10, -5). The callback should update this parameter to reflect the new absolute brightness level after the adjustment.
*   **Return Type**: `bool`
    *   `true`: The request was handled successfully.
    *   `false`: The request could not be handled properly.
*   **Usage Example**:
    ```cpp
    bool onAdjustBrightness(const String &deviceId, int &brightnessDelta) {
      Serial.printf("Device %s adjusted brightness by %d\r\n", deviceId.c_str(), brightnessDelta);
      // Your code to adjust the device brightness
      // Update brightnessDelta to the new absolute brightness
      return true;
    }
    // In setup:
    // myLight.onAdjustBrightness(onAdjustBrightness);
    ```

## Public Methods

### `BrightnessController()`
*   **Purpose**: Constructor for the `BrightnessController`. It initializes an `EventLimiter` and registers a request handler with the derived device class to process brightness-related requests.
*   **Parameters**: None
*   **Return Type**: None

### `onBrightness(BrightnessCallback cb)`
*   **Purpose**: Sets the callback function that will be invoked when the SinricPro server sends a `setBrightness` command to the device.
*   **Parameters**:
    *   `cb`: A `BrightnessCallback` function pointer or lambda that will handle the absolute brightness setting request.
*   **Return Type**: `void`

### `onAdjustBrightness(AdjustBrightnessCallback cb)`
*   **Purpose**: Sets the callback function that will be invoked when the SinricPro server sends an `adjustBrightness` command to the device.
*   **Parameters**:
    *   `cb`: An `AdjustBrightnessCallback` function pointer or lambda that will handle the relative brightness adjustment request.
*   **Return Type**: `void`

### `sendBrightnessEvent(int brightness, String cause = FSTR_SINRICPRO_PHYSICAL_INTERACTION)`
*   **Purpose**: Sends a `setBrightness` event to the SinricPro server, informing it about the device's current absolute brightness level. This is typically called when the device's brightness changes locally.
*   **Parameters**:
    *   `brightness`: An `int` indicating the current absolute brightness level (0-100).
    *   `cause`: (Optional) A `String` describing the reason for the event (default is `"PHYSICAL_INTERACTION"`).
*   **Return Type**: `bool`
    *   `true`: The event was sent successfully.
    *   `false`: The event was not sent, possibly due to event rate limiting.
*   **Usage Example**:
    ```cpp
    // After setting brightness locally
    // myLight.sendBrightnessEvent(50);
    ```

## Protected Methods

### `handleBrightnessController(SinricProRequest &request)`
*   **Purpose**: Internal method used by the `BrightnessController` to process incoming `SinricProRequest` objects. It checks if the request is a `setBrightness` or `adjustBrightness` action and, if so, invokes the corresponding registered callback.
*   **Parameters**:
    *   `request`: A reference to the `SinricProRequest` object received from the server.
*   **Return Type**: `bool` (Indicates if the request was handled by this controller.)

## Dependencies
*   `../SinricProRequest.h`
*   `../EventLimiter.h`
*   `../SinricProStrings.h`
*   `../SinricProNamespace.h`

## Tags
`SinricPro`, `SDK`, `Capability`, `Brightness`, `Controller`, `Smart Home`, `Light`, `ESP8266`, `ESP32`
