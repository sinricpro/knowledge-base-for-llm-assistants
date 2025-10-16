# ThermostatController

## Overview
The `ThermostatController` is a comprehensive template class that provides full functionality for SinricPro devices acting as thermostats. It enables devices to receive commands for setting and adjusting target temperatures, as well as changing operational modes (e.g., AUTO, COOL, HEAT). It also allows the device to report its current thermostat mode and target temperature to the SinricPro server.

## Template Parameters
*   `T`: The device type that this controller is attached to. This allows the controller to interact with the derived device's properties and methods (e.g., `deviceId`, `prepareEvent`, `sendEvent`).

## Callback Handlers (Typedefs)

### `ThermostatModeCallback`
*   **Purpose**: Defines the signature for the callback function that is invoked when the device receives a `setThermostatMode` request from the SinricPro server.
*   **Signature**: `std::function<bool(const String &deviceId, String &mode)>`
*   **Parameters**:
    *   `deviceId`: A `String` containing the unique ID of the device.
    *   `mode`: A `String` reference. On input, it represents the requested thermostat mode (e.g., `"AUTO"`, `"COOL"`, `"HEAT"`). The callback should update this parameter to reflect the actual mode set by the device.
*   **Return Type**: `bool`
    *   `true`: The request was handled successfully.
    *   `false`: The request could not be handled properly.
*   **Usage Example**:
    ```cpp
    bool onThermostatMode(const String &deviceId, String &mode) {
      Serial.printf("Device %s set thermostat mode to %s\r\n", deviceId.c_str(), mode.c_str());
      // Your code to set the thermostat mode
      return true;
    }
    // In setup:
    // myThermostat.onThermostatMode(onThermostatMode);
    ```

### `SetTargetTemperatureCallback`
*   **Purpose**: Defines the signature for the callback function that is invoked when the device receives a `targetTemperature` request to set an absolute target temperature.
*   **Signature**: `std::function<bool(const String &deviceId, float &temperature)>`
*   **Parameters**:
    *   `deviceId`: A `String` containing the unique ID of the device.
    *   `temperature`: A `float` reference. On input, it represents the requested absolute target temperature. The callback should update this parameter to reflect the actual target temperature set by the device.
*   **Return Type**: `bool`
    *   `true`: The request was handled successfully.
    *   `false`: The request could not be handled properly.
*   **Usage Example**:
    ```cpp
    bool onTargetTemperature(const String &deviceId, float &temperature) {
      Serial.printf("Device %s set target temperature to %.1f°C\r\n", deviceId.c_str(), temperature);
      // Your code to set the target temperature
      return true;
    }
    // In setup:
    // myThermostat.onTargetTemperature(onTargetTemperature);
    ```

### `AdjustTargetTemperatureCallback`
*   **Purpose**: Defines the signature for the callback function that is invoked when the device receives an `adjustTargetTemperature` request to adjust the target temperature relatively.
*   **Signature**: `std::function<bool(const String &deviceId, float &temperatureDelta)>`
*   **Parameters**:
    *   `deviceId`: A `String` containing the unique ID of the device.
    *   `temperatureDelta`: A `float` reference. On input, it represents the relative change in temperature. The callback should update this parameter to reflect the new absolute target temperature after the adjustment.
*   **Return Type**: `bool`
    *   `true`: The request was handled successfully.
    *   `false`: The request could not be handled properly.
*   **Usage Example**:
    ```cpp
    bool onAdjustTargetTemperature(const String &deviceId, float &temperatureDelta) {
      Serial.printf("Device %s adjusted target temperature by %.1f°C\r\n", deviceId.c_str(), temperatureDelta);
      // Your code to adjust the target temperature
      return true;
    }
    // In setup:
    // myThermostat.onAdjustTargetTemperature(onAdjustTargetTemperature);
    ```

## Public Methods

### `ThermostatController()`
*   **Purpose**: Constructor for the `ThermostatController`. It initializes event limiters for different event types and registers a request handler with the derived device class to process thermostat-related requests.
*   **Parameters**: None
*   **Return Type**: None

### `onThermostatMode(ThermostatModeCallback cb)`
*   **Purpose**: Sets the callback function that will be invoked when the SinricPro server sends a `setThermostatMode` command to the device.
*   **Parameters**:
    *   `cb`: A `ThermostatModeCallback` function pointer or lambda that will handle the thermostat mode setting request.
*   **Return Type**: `void`

### `onTargetTemperature(SetTargetTemperatureCallback cb)`
*   **Purpose**: Sets the callback function that will be invoked when the SinricPro server sends a `targetTemperature` command to the device.
*   **Parameters**:
    *   `cb`: A `SetTargetTemperatureCallback` function pointer or lambda that will handle the absolute target temperature setting request.
*   **Return Type**: `void`

### `onAdjustTargetTemperature(AdjustTargetTemperatureCallback cb)`
*   **Purpose**: Sets the callback function that will be invoked when the SinricPro server sends an `adjustTargetTemperature` command to the device.
*   **Parameters**:
    *   `cb`: An `AdjustTargetTemperatureCallback` function pointer or lambda that will handle the relative target temperature adjustment request.
*   **Return Type**: `void`

### `sendThermostatModeEvent(String thermostatMode, String cause = FSTR_SINRICPRO_PHYSICAL_INTERACTION)`
*   **Purpose**: Sends a `thermostatMode` event to the SinricPro server, informing it about the device's current thermostat mode. This is typically called when the device's mode changes locally.
*   **Parameters**:
    *   `thermostatMode`: A `String` indicating the current thermostat mode (e.g., `"AUTO"`, `"COOL"`, `"HEAT"`).
    *   `cause`: (Optional) A `String` describing the reason for the event (default is `"PHYSICAL_INTERACTION"`).
*   **Return Type**: `bool`
    *   `true`: The event was sent successfully.
    *   `false`: The event was not sent, possibly due to event rate limiting.
*   **Usage Example**:
    ```cpp
    // After changing mode locally
    // myThermostat.sendThermostatModeEvent("HEAT");
    ```

### `sendTargetTemperatureEvent(float temperature, String cause = FSTR_SINRICPRO_PHYSICAL_INTERACTION)`
*   **Purpose**: Sends a `targetTemperature` event to the SinricPro server, informing it about the device's current target temperature. This is typically called when the device's target temperature changes locally.
*   **Parameters**:
    *   `temperature`: A `float` indicating the current target temperature.
    *   `cause`: (Optional) A `String` describing the reason for the event (default is `"PHYSICAL_INTERACTION"`).
*   **Return Type**: `bool`
    *   `true`: The event was sent successfully.
    *   `false`: The event was not sent, possibly due to event rate limiting.
*   **Usage Example**:
    ```cpp
    // After setting target temperature locally
    // myThermostat.sendTargetTemperatureEvent(22.5);
    ```

## Protected Methods

### `handleThermostatController(SinricProRequest &request)`
*   **Purpose**: Internal method used by the `ThermostatController` to process incoming `SinricProRequest` objects. It checks if the request is a `targetTemperature`, `adjustTargetTemperature`, or `setThermostatMode` action and, if so, invokes the corresponding registered callback.
*   **Parameters**:
    *   `request`: A reference to the `SinricProRequest` object received from the server.
*   **Return Type**: `bool` (Indicates if the request was handled by this controller.)

## Dependencies
*   `../SinricProRequest.h`
*   `../EventLimiter.h`
*   `../SinricProStrings.h`
*   `../SinricProNamespace.h`

## Tags
`SinricPro`, `SDK`, `Capability`, `Thermostat`, `Temperature Control`, `Mode Control`, `Smart Home`, `HVAC`, `ESP8266`, `ESP32`
