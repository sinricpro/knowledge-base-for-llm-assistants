# ColorTemperatureController

## Overview
The `ColorTemperatureController` is a template class that provides functionality for SinricPro devices to control and report their color temperature. It supports setting an absolute color temperature, as well as incremental increases and decreases. This controller is essential for smart lighting devices that offer tunable white light.

## Template Parameters
*   `T`: The derived class that incorporates `ColorTemperatureController`. This allows the controller to interact with the derived device's properties and methods (e.g., `deviceId`, `prepareEvent`, `sendEvent`).

## Callback Handlers (Typedefs)

### `ColorTemperatureCallback`
*   **Purpose**: Defines the signature for the callback function that is invoked when the device receives a `setColorTemperature` request from the SinricPro server.
*   **Signature**: `std::function<bool(const String &deviceId, int &colorTemperature)>`
*   **Parameters**:
    *   `deviceId`: A `String` containing the unique ID of the device.
    *   `colorTemperature`: An `int` reference. On input, it represents the requested absolute color temperature (e.g., 2200K, 4000K, 7000K). The callback should update this parameter to reflect the actual color temperature set by the device.
*   **Return Type**: `bool`
    *   `true`: The request was handled successfully.
    *   `false`: The request could not be handled properly.
*   **Usage Example**:
    ```cpp
    bool onColorTemperature(const String &deviceId, int &colorTemperature) {
      Serial.printf("Device %s set color temperature to %dK\r\n", deviceId.c_str(), colorTemperature);
      // Your code to set the device color temperature
      // If successful, ensure 'colorTemperature' reflects the actual state
      return true;
    }
    // In setup:
    // myLight.onColorTemperature(onColorTemperature);
    ```

### `IncreaseColorTemperatureCallback`
*   **Purpose**: Defines the signature for the callback function that is invoked when the device receives an `increaseColorTemperature` request from the SinricPro server.
*   **Signature**: `std::function<bool(const String &deviceId, int &colorTemperature)>`
*   **Parameters**:
    *   `deviceId`: A `String` containing the unique ID of the device.
    *   `colorTemperature`: An `int` reference. On input, it's typically `1` (indicating a request to increase). The callback should update this parameter to reflect the new absolute color temperature after the increase.
*   **Return Type**: `bool`
    *   `true`: The request was handled successfully.
    *   `false`: The request could not be handled properly.
*   **Usage Example**:
    ```cpp
    bool onIncreaseColorTemperature(const String &deviceId, int &colorTemperature) {
      Serial.printf("Device %s increased color temperature\r\n", deviceId.c_str());
      // Your code to increase the device color temperature
      // Update colorTemperature to the new absolute value
      return true;
    }
    // In setup:
    // myLight.onIncreaseColorTemperature(onIncreaseColorTemperature);
    ```

### `DecreaseColorTemperatureCallback`
*   **Purpose**: Defines the signature for the callback function that is invoked when the device receives a `decreaseColorTemperature` request from the SinricPro server.
*   **Signature**: `std::function<bool(const String &deviceId, int &colorTemperature)>`
*   **Parameters**:
    *   `deviceId`: A `String` containing the unique ID of the device.
    *   `colorTemperature`: An `int` reference. On input, it's typically `-1` (indicating a request to decrease). The callback should update this parameter to reflect the new absolute color temperature after the decrease.
*   **Return Type**: `bool`
    *   `true`: The request was handled successfully.
    *   `false`: The request could not be handled properly.
*   **Usage Example**:
    ```cpp
    bool onDecreaseColorTemperature(const String &deviceId, int &colorTemperature) {
      Serial.printf("Device %s decreased color temperature\r\n", deviceId.c_str());
      // Your code to decrease the device color temperature
      // Update colorTemperature to the new absolute value
      return true;
    }
    // In setup:
    // myLight.onDecreaseColorTemperature(onDecreaseColorTemperature);
    ```

## Public Methods

### `ColorTemperatureController()`
*   **Purpose**: Constructor for the `ColorTemperatureController`. It initializes an `EventLimiter` and registers a request handler with the derived device class to process color temperature-related requests.
*   **Parameters**: None
*   **Return Type**: None

### `onColorTemperature(ColorTemperatureCallback cb)`
*   **Purpose**: Sets the callback function that will be invoked when the SinricPro server sends a `setColorTemperature` command to the device.
*   **Parameters**:
    *   `cb`: A `ColorTemperatureCallback` function pointer or lambda that will handle the absolute color temperature setting request.
*   **Return Type**: `void`

### `onIncreaseColorTemperature(IncreaseColorTemperatureCallback cb)`
*   **Purpose**: Sets the callback function that will be invoked when the SinricPro server sends an `increaseColorTemperature` command to the device.
*   **Parameters**:
    *   `cb`: An `IncreaseColorTemperatureCallback` function pointer or lambda that will handle the request to increase color temperature.
*   **Return Type**: `void`

### `onDecreaseColorTemperature(DecreaseColorTemperatureCallback cb)`
*   **Purpose**: Sets the callback function that will be invoked when the SinricPro server sends a `decreaseColorTemperature` command to the device.
*   **Parameters**:
    *   `cb`: A `DecreaseColorTemperatureCallback` function pointer or lambda that will handle the request to decrease color temperature.
*   **Return Type**: `void`

### `sendColorTemperatureEvent(int colorTemperature, String cause = FSTR_SINRICPRO_PHYSICAL_INTERACTION)`
*   **Purpose**: Sends a `setColorTemperature` event to the SinricPro server, informing it about the device's current absolute color temperature. This is typically called when the device's color temperature changes locally.
*   **Parameters**:
    *   `colorTemperature`: An `int` indicating the current absolute color temperature.
    *   `cause`: (Optional) A `String` describing the reason for the event (default is `"PHYSICAL_INTERACTION"`).
*   **Return Type**: `bool`
    *   `true`: The event was sent successfully.
    *   `false`: The event was not sent, possibly due to event rate limiting.
*   **Usage Example**:
    ```cpp
    // After setting color temperature locally
    // myLight.sendColorTemperatureEvent(4000);
    ```

## Protected Methods

### `handleColorTemperatureController(SinricProRequest &request)`
*   **Purpose**: Internal method used by the `ColorTemperatureController` to process incoming `SinricProRequest` objects. It checks if the request is a `setColorTemperature`, `increaseColorTemperature`, or `decreaseColorTemperature` action and, if so, invokes the corresponding registered callback.
*   **Parameters**:
    *   `request`: A reference to the `SinricProRequest` object received from the server.
*   **Return Type**: `bool` (Indicates if the request was handled by this controller.)

## Dependencies
*   `../SinricProRequest.h`
*   `../EventLimiter.h`
*   `../SinricProStrings.h`
*   `../SinricProNamespace.h`

## Tags
`SinricPro`, `SDK`, `Capability`, `Color Temperature`, `Controller`, `Smart Home`, `Light`, `ESP8266`, `ESP32`
