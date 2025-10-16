# PowerLevelController

## Overview
The `PowerLevelController` is a template class that provides functionality for SinricPro devices to control and report their power level, typically on a scale from 0 to 100. This capability is similar to `PercentageController` but is specifically named for power-related adjustments, such as the intensity of a heater or the output of a power supply. It supports setting an absolute power level and adjusting it relatively.

## Template Parameters
*   `T`: The derived class that incorporates `PowerLevelController`. This allows the controller to interact with the derived device's properties and methods (e.g., `deviceId`, `prepareEvent`, `sendEvent`).

## Callback Handlers (Typedefs)

### `SetPowerLevelCallback`
*   **Purpose**: Defines the signature for the callback function that is invoked when the device receives a `setPowerLevel` request from the SinricPro server.
*   **Signature**: `std::function<bool(const String &deviceId, int &powerLevel)>`
*   **Parameters**:
    *   `deviceId`: A `String` containing the unique ID of the device.
    *   `powerLevel`: An `int` reference (0-100). On input, it represents the requested absolute power level. The callback should update this parameter to reflect the actual power level set by the device.
*   **Return Type**: `bool`
    *   `true`: The request was handled successfully.
    *   `false`: The request could not be handled properly.
*   **Usage Example**:
    ```cpp
    bool onPowerLevel(const String &deviceId, int &powerLevel) {
      Serial.printf("Device %s set power level to %d\r\n", deviceId.c_str(), powerLevel);
      // Your code to set the device power level
      return true;
    }
    // In setup:
    // myHeater.onPowerLevel(onPowerLevel);
    ```

### `AdjustPowerLevelCallback`
*   **Purpose**: Defines the signature for the callback function that is invoked when the device receives an `adjustPowerLevel` request from the SinricPro server.
*   **Signature**: `std::function<bool(const String &deviceId, int &powerLevelDelta)>`
*   **Parameters**:
    *   `deviceId`: A `String` containing the unique ID of the device.
    *   `powerLevelDelta`: An `int` reference. On input, it represents the relative change in power level (-100 to 100). The callback should update this parameter to reflect the new absolute power level after the adjustment.
*   **Return Type**: `bool`
    *   `true`: The request was handled successfully.
    *   `false`: The request could not be handled properly.
*   **Usage Example**:
    ```cpp
    bool onAdjustPowerLevel(const String &deviceId, int &powerLevelDelta) {
      Serial.printf("Device %s adjusted power level by %d\r\n", deviceId.c_str(), powerLevelDelta);
      // Your code to adjust the device power level
      return true;
    }
    // In setup:
    // myHeater.onAdjustPowerLevel(onAdjustPowerLevel);
    ```

## Public Methods

### `PowerLevelController()`
*   **Purpose**: Constructor for the `PowerLevelController`. It initializes an `EventLimiter` and registers a request handler with the derived device class to process power level-related requests.
*   **Parameters**: None
*   **Return Type**: None

### `onPowerLevel(SetPowerLevelCallback cb)`
*   **Purpose**: Sets the callback function that will be invoked when the SinricPro server sends a `setPowerLevel` command to the device.
*   **Parameters**:
    *   `cb`: A `SetPowerLevelCallback` function pointer or lambda that will handle the absolute power level setting request.
*   **Return Type**: `void`

### `onAdjustPowerLevel(AdjustPowerLevelCallback cb)`
*   **Purpose**: Sets the callback function that will be invoked when the SinricPro server sends an `adjustPowerLevel` command to the device.
*   **Parameters**:
    *   `cb`: An `AdjustPowerLevelCallback` function pointer or lambda that will handle the relative power level adjustment request.
*   **Return Type**: `void`

### `sendPowerLevelEvent(int powerLevel, String cause = FSTR_SINRICPRO_PHYSICAL_INTERACTION)`
*   **Purpose**: Sends a `setPowerLevel` event to the SinricPro server, informing it about the device's current absolute power level. This is typically called when the device's power level changes locally.
*   **Parameters**:
    *   `powerLevel`: An `int` indicating the current absolute power level (0-100).
    *   `cause`: (Optional) A `String` describing the reason for the event (default is `"PHYSICAL_INTERACTION"`).
*   **Return Type**: `bool`
    *   `true`: The event was sent successfully.
    *   `false`: The event was not sent, possibly due to event rate limiting.
*   **Usage Example**:
    ```cpp
    // After setting power level locally
    // myHeater.sendPowerLevelEvent(80);
    ```

## Protected Methods

### `handlePowerLevelController(SinricProRequest &request)`
*   **Purpose**: Internal method used by the `PowerLevelController` to process incoming `SinricProRequest` objects. It checks if the request is a `setPowerLevel` or `adjustPowerLevel` action and, if so, invokes the corresponding registered callback.
*   **Parameters**:
    *   `request`: A reference to the `SinricProRequest` object received from the server.
*   **Return Type**: `bool` (Indicates if the request was handled by this controller.)

## Dependencies
*   `../SinricProRequest.h`
*   `../EventLimiter.h`
*   `../SinricProStrings.h`
*   `../SinricProNamespace.h`

## Tags
`SinricPro`, `SDK`, `Capability`, `Power Level`, `Controller`, `Smart Home`, `Heater`, `Dimmer`, `ESP8266`, `ESP32`
