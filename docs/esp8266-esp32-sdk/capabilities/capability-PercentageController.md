# PercentageController

## Overview
The `PercentageController` is a template class that provides functionality for SinricPro devices to operate on a percentage scale. This is useful for devices where a state can be represented as a value between 0 and 100, such as fan speed, dimmer levels, or water flow. It supports setting an absolute percentage and adjusting it relatively, as well as reporting the current percentage to the SinricPro server.

## Template Parameters
*   `T`: The derived class that incorporates `PercentageController`. This allows the controller to interact with the derived device's properties and methods (e.g., `deviceId`, `prepareEvent`, `sendEvent`).

## Callback Handlers (Typedefs)

### `SetPercentageCallback`
*   **Purpose**: Defines the signature for the callback function that is invoked when the device receives a `setPercentage` request from the SinricPro server.
*   **Signature**: `std::function<bool(const String &deviceId, int &percentage)>`
*   **Parameters**:
    *   `deviceId`: A `String` containing the unique ID of the device.
    *   `percentage`: An `int` reference. On input, it represents the requested absolute percentage (0-100). The callback should update this parameter to reflect the actual percentage set by the device.
*   **Return Type**: `bool`
    *   `true`: The request was handled successfully.
    *   `false`: The request could not be handled properly.
*   **Usage Example**:
    ```cpp
    bool onSetPercentage(const String &deviceId, int &percentage) {
      Serial.printf("Device %s set percentage to %d\r\n", deviceId.c_str(), percentage);
      // Your code to set the device percentage
      return true;
    }
    // In setup:
    // myFan.onSetPercentage(onSetPercentage);
    ```

### `AdjustPercentageCallback`
*   **Purpose**: Defines the signature for the callback function that is invoked when the device receives an `adjustPercentage` request from the SinricPro server.
*   **Signature**: `std::function<bool(const String &deviceId, int &percentageDelta)>`
*   **Parameters**:
    *   `deviceId`: A `String` containing the unique ID of the device.
    *   `percentageDelta`: An `int` reference. On input, it represents the relative percentage change (e.g., +10, -5). The callback should update this parameter to reflect the new absolute percentage value after the adjustment.
*   **Return Type**: `bool`
    *   `true`: The request was handled successfully.
    *   `false`: The request could not be handled properly.
*   **Usage Example**:
    ```cpp
    bool onAdjustPercentage(const String &deviceId, int &percentageDelta) {
      Serial.printf("Device %s adjusted percentage by %d\r\n", deviceId.c_str(), percentageDelta);
      // Your code to adjust the device percentage
      return true;
    }
    // In setup:
    // myFan.onAdjustPercentage(onAdjustPercentage);
    ```

## Public Methods

### `PercentageController()`
*   **Purpose**: Constructor for the `PercentageController`. It initializes an `EventLimiter` and registers a request handler with the derived device class to process percentage-related requests.
*   **Parameters**: None
*   **Return Type**: None

### `onSetPercentage(SetPercentageCallback cb)`
*   **Purpose**: Sets the callback function that will be invoked when the SinricPro server sends a `setPercentage` command to the device.
*   **Parameters**:
    *   `cb`: A `SetPercentageCallback` function pointer or lambda that will handle the absolute percentage setting request.
*   **Return Type**: `void`

### `onAdjustPercentage(AdjustPercentageCallback cb)`
*   **Purpose**: Sets the callback function that will be invoked when the SinricPro server sends an `adjustPercentage` command to the device.
*   **Parameters**:
    *   `cb`: An `AdjustPercentageCallback` function pointer or lambda that will handle the relative percentage adjustment request.
*   **Return Type**: `void`

### `sendSetPercentageEvent(int percentage, String cause = FSTR_SINRICPRO_PHYSICAL_INTERACTION)`
*   **Purpose**: Sends a `setPercentage` event to the SinricPro server, informing it about the device's current absolute percentage. This is typically called when the device's percentage changes locally.
*   **Parameters**:
    *   `percentage`: An `int` indicating the current absolute percentage (0-100).
    *   `cause`: (Optional) A `String` describing the reason for the event (default is `"PHYSICAL_INTERACTION"`).
*   **Return Type**: `bool`
    *   `true`: The event was sent successfully.
    *   `false`: The event was not sent, possibly due to event rate limiting.
*   **Usage Example**:
    ```cpp
    // After setting fan speed locally
    // myFan.sendSetPercentageEvent(75);
    ```

## Protected Methods

### `handlePercentageController(SinricProRequest &request)`
*   **Purpose**: Internal method used by the `PercentageController` to process incoming `SinricProRequest` objects. It checks if the request is a `setPercentage` or `adjustPercentage` action and, if so, invokes the corresponding registered callback.
*   **Parameters**:
    *   `request`: A reference to the `SinricProRequest` object received from the server.
*   **Return Type**: `bool` (Indicates if the request was handled by this controller.)

## Dependencies
*   `../SinricProRequest.h`
*   `../EventLimiter.h`
*   `../SinricProStrings.h`
*   `../SinricProNamespace.h`

## Tags
`SinricPro`, `SDK`, `Capability`, `Percentage`, `Controller`, `Smart Home`, `Fan`, `Dimmer`, `ESP8266`, `ESP32`
