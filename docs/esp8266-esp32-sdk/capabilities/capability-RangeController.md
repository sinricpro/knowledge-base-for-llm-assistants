# RangeController

## Overview
The `RangeController` is a highly versatile template class that provides comprehensive functionality for SinricPro devices operating within a numerical range. This can apply to various device types, such as dimmers with extended ranges, thermostats with custom temperature scales, or any device where a value needs to be set or adjusted within a defined minimum and maximum. It supports setting absolute values, adjusting values relatively, and handles both integer and floating-point values, including support for multiple instances (e.g., different zones or sub-components within a device).

## Template Parameters
*   `T`: The derived class that incorporates `RangeController`. This allows the controller to interact with the derived device's properties and methods (e.g., `deviceId`, `prepareEvent`, `sendEvent`).

## Callback Handlers (Typedefs and Structs)

### `GenericRangeValueCallback_int`
*   **Purpose**: Function pointer type for callbacks handling integer range values for a specific instance.
*   **Signature**: `bool (*)(const String &deviceId, const String &instance, int &rangeValue)`

### `GenericRangeValueCallback_float`
*   **Purpose**: Function pointer type for callbacks handling floating-point range values for a specific instance.
*   **Signature**: `bool (*)(const String &deviceId, const String &instance, float &rangeValue)`

### `GenericRangeValueCallback` (Struct)
*   **Purpose**: A union-like structure used internally to store either an integer or floating-point generic callback, allowing the controller to handle different data types dynamically.
*   **Members**:
    *   `type`: An enum indicating whether the stored callback is `type_int` or `type_float`.
    *   `cb_int`: Stores a `GenericRangeValueCallback_int`.
    *   `cb_float`: Stores a `GenericRangeValueCallback_float`.

### `SetRangeValueCallback`
*   **Purpose**: Function pointer type for callbacks handling general (non-instance specific) integer range values when setting an absolute value.
*   **Signature**: `bool (*)(const String &deviceId, int &rangeValue)`

### `GenericSetRangeValueCallback_int`
*   **Purpose**: Alias for `GenericRangeValueCallback_int`, used for clarity when setting instance-specific integer range values.

### `GenericSetRangeValueCallback_float`
*   **Purpose**: Alias for `GenericRangeValueCallback_float`, used for clarity when setting instance-specific floating-point range values.

### `AdjustRangeValueCallback`
*   **Purpose**: Function pointer type for callbacks handling general (non-instance specific) integer range values when adjusting relatively.
*   **Signature**: `bool (*)(const String &deviceId, int &rangeValueDelta)`

### `GenericAdjustRangeValueCallback_int`
*   **Purpose**: Alias for `GenericRangeValueCallback_int`, used for clarity when adjusting instance-specific integer range values.

### `GenericAdjustRangeValueCallback_float`
*   **Purpose**: Alias for `GenericRangeValueCallback_float`, used for clarity when adjusting instance-specific floating-point range values.

## Public Methods

### `RangeController()`
*   **Purpose**: Constructor for the `RangeController`. It initializes event limiters and registers a request handler with the derived device class to process range-related requests.
*   **Parameters**: None
*   **Return Type**: None

### `onRangeValue(SetRangeValueCallback cb)`
*   **Purpose**: Sets the callback function for general (non-instance specific) `setRangeValue` requests with integer values.
*   **Parameters**:
    *   `cb`: A `SetRangeValueCallback` function pointer or lambda.
*   **Return Type**: `void`

### `onRangeValue(const String& instance, GenericSetRangeValueCallback_int cb)`
*   **Purpose**: Sets the callback function for instance-specific `setRangeValue` requests with integer values.
*   **Parameters**:
    *   `instance`: The name of the instance.
    *   `cb`: A `GenericSetRangeValueCallback_int` function pointer or lambda.
*   **Return Type**: `void`

### `onRangeValue(const String& instance, GenericSetRangeValueCallback_float cb)`
*   **Purpose**: Sets the callback function for instance-specific `setRangeValue` requests with floating-point values.
*   **Parameters**:
    *   `instance`: The name of the instance.
    *   `cb`: A `GenericSetRangeValueCallback_float` function pointer or lambda.
*   **Return Type**: `void`

### `onAdjustRangeValue(AdjustRangeValueCallback cb)`
*   **Purpose**: Sets the callback function for general (non-instance specific) `adjustRangeValue` requests with integer values.
*   **Parameters**:
    *   `cb`: An `AdjustRangeValueCallback` function pointer or lambda.
*   **Return Type**: `void`

### `onAdjustRangeValue(const String& instance, GenericAdjustRangeValueCallback_int cb)`
*   **Purpose**: Sets the callback function for instance-specific `adjustRangeValue` requests with integer values.
*   **Parameters**:
    *   `instance`: The name of the instance.
    *   `cb`: A `GenericAdjustRangeValueCallback_int` function pointer or lambda.
*   **Return Type**: `void`

### `onAdjustRangeValue(const String& instance, GenericAdjustRangeValueCallback_float cb)`
*   **Purpose**: Sets the callback function for instance-specific `adjustRangeValue` requests with floating-point values.
*   **Parameters**:
    *   `instance`: The name of the instance.
    *   `cb`: A `GenericAdjustRangeValueCallback_float` function pointer or lambda.
*   **Return Type**: `void`

### `sendRangeValueEvent(int rangeValue, String cause = FSTR_SINRICPRO_PHYSICAL_INTERACTION)`
*   **Purpose**: Sends a general `setRangeValue` event with an integer value to the SinricPro server.
*   **Parameters**:
    *   `rangeValue`: The current integer range value.
    *   `cause`: (Optional) Reason for the event (default: `"PHYSICAL_INTERACTION"`).
*   **Return Type**: `bool` (Whether the event was sent successfully).

### `sendRangeValueEvent(const String& instance, int rangeValue, String cause = FSTR_SINRICPRO_PHYSICAL_INTERACTION)`
*   **Purpose**: Sends an instance-specific `setRangeValue` event with an integer value to the SinricPro server.
*   **Parameters**:
    *   `instance`: The name of the instance.
    *   `rangeValue`: The current integer range value.
    *   `cause`: (Optional) Reason for the event (default: `"PHYSICAL_INTERACTION"`).
*   **Return Type**: `bool` (Whether the event was sent successfully).

### `sendRangeValueEvent(const String& instance, float rangeValue, String cause = FSTR_SINRICPRO_PHYSICAL_INTERACTION)`
*   **Purpose**: Sends an instance-specific `setRangeValue` event with a floating-point value to the SinricPro server.
*   **Parameters**:
    *   `instance`: The name of the instance.
    *   `rangeValue`: The current floating-point range value.
    *   `cause`: (Optional) Reason for the event (default: `"PHYSICAL_INTERACTION"`).
*   **Return Type**: `bool` (Whether the event was sent successfully).

## Protected Methods

### `handleRangeController(SinricProRequest &request)`
*   **Purpose**: Internal method used by the `RangeController` to process incoming `SinricProRequest` objects. It handles both `setRangeValue` and `adjustRangeValue` actions, distinguishing between general and instance-specific requests, and between integer and floating-point values, then invokes the appropriate registered callback.
*   **Parameters**:
    *   `request`: The incoming request to process.
*   **Return Type**: `bool` (Whether the request was handled successfully).

## Dependencies
*   `../SinricProRequest.h`
*   `../EventLimiter.h`
*   `../SinricProStrings.h`
*   `../SinricProNamespace.h`

## Tags
`SinricPro`, `SDK`, `Capability`, `Range`, `Controller`, `Smart Home`, `Thermostat`, `Dimmer`, `ESP8266`, `ESP32`
