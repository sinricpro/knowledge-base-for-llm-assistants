# OpenCloseController

## Overview
The `OpenCloseController` is a comprehensive template class that provides advanced control and reporting capabilities for devices that can be opened or closed, such as blinds, curtains, or garage doors. It supports setting an absolute open percentage, adjusting it relatively, and handling directional open/close operations (e.g., UP, DOWN, LEFT, RIGHT). This controller allows for fine-grained control over the device's open/close state.

## Template Parameters
*   `T`: The derived class that incorporates `OpenCloseController`. This allows the controller to interact with the derived device's properties and methods (e.g., `deviceId`, `prepareEvent`, `sendEvent`).

## Callback Handlers (Typedefs)

### `OpenCloseCallback`
*   **Purpose**: Defines the signature for the callback function invoked when the device receives a `setOpenClose` request for an absolute open percentage.
*   **Signature**: `std::function<bool(const String &deviceId, int &openPercent)>`
*   **Parameters**:
    *   `deviceId`: A `String` containing the unique ID of the device.
    *   `openPercent`: An `int` reference (0-100). On input, it's the requested percentage of how open the device should be (0 = closed, 100 = fully open). The callback should update this parameter to reflect the actual percentage set by the device.
*   **Return Type**: `bool` (`true` if handled successfully, `false` otherwise).

### `AdjustOpenCloseCallback`
*   **Purpose**: Defines the signature for the callback function invoked when the device receives an `adjustOpenClose` request for a relative percentage change.
*   **Signature**: `std::function<bool(const String &deviceId, int &openRelativePercent)>`
*   **Parameters**:
    *   `deviceId`: A `String` containing the unique ID of the device.
    *   `openRelativePercent`: An `int` reference. On input, it represents the relative percentage change to apply. The callback should update this parameter to contain the new absolute percentage value the device has been set to.
*   **Return Type**: `bool` (`true` if handled successfully, `false` otherwise).

### `DirectionOpenCloseCallback`
*   **Purpose**: Defines the signature for the callback function invoked when the device receives a `setOpenClose` request with direction information.
*   **Signature**: `std::function<bool(const String &deviceId, const String &openDirection, int &openPercent)>`
*   **Parameters**:
    *   `deviceId`: A `String` containing the unique ID of the device.
    *   `openDirection`: A `String` indicating the direction in which to open (e.g., `"UP"`, `"DOWN"`, `"LEFT"`, `"RIGHT"`, `"IN"`, `"OUT"`).
    *   `openPercent`: An `int` reference (0-100). On input, it's the requested percentage. The callback should update this parameter to reflect the actual percentage set by the device.
*   **Return Type**: `bool` (`true` if handled successfully, `false` otherwise).

### `AdjustDirectionOpenCloseCallback`
*   **Purpose**: Defines the signature for the callback function invoked when the device receives an `adjustOpenClose` request with direction information.
*   **Signature**: `std::function<bool(const String &deviceId, const String &openDirection, int &openRelativePercent)>`
*   **Parameters**:
    *   `deviceId`: A `String` containing the unique ID of the device.
    *   `openDirection`: A `String` indicating the direction in which to open.
    *   `openRelativePercent`: An `int` reference. On input, it represents the relative percentage change. The callback should update this parameter to contain the new absolute percentage value the device has been set to.
*   **Return Type**: `bool` (`true` if handled successfully, `false` otherwise).

## Public Methods

### `OpenCloseController()`
*   **Purpose**: Constructor for the `OpenCloseController`. It initializes an `EventLimiter` and registers a request handler with the derived device class to process open/close related requests.
*   **Parameters**: None
*   **Return Type**: None

### `onOpenClose(OpenCloseCallback cb)`
*   **Purpose**: Sets the callback function for simple open/close operations based on an absolute percentage.
*   **Parameters**:
    *   `cb`: A `OpenCloseCallback` function pointer or lambda.
*   **Return Type**: `void`

### `onDirectionOpenClose(DirectionOpenCloseCallback cb)`
*   **Purpose**: Sets the callback function for directional open/close operations.
*   **Parameters**:
    *   `cb`: A `DirectionOpenCloseCallback` function pointer or lambda.
*   **Return Type**: `void`

### `onAdjustOpenClose(AdjustOpenCloseCallback cb)`
*   **Purpose**: Sets the callback function for relative adjustments to the open/close status.
*   **Parameters**:
    *   `cb`: A `AdjustOpenCloseCallback` function pointer or lambda.
*   **Return Type**: `void`

### `onAdjustDirectionOpenClose(AdjustDirectionOpenCloseCallback cb)`
*   **Purpose**: Sets the callback function for directional relative adjustments to the open/close status.
*   **Parameters**:
    *   `cb`: A `AdjustDirectionOpenCloseCallback` function pointer or lambda.
*   **Return Type**: `void`

### `sendOpenCloseEvent(int openPercent, String cause = FSTR_SINRICPRO_PHYSICAL_INTERACTION)`
*   **Purpose**: Sends an event to the SinricPro server to update the device's open/close status based on an absolute percentage.
*   **Parameters**:
    *   `openPercent`: Current open percentage (0-100).
    *   `cause`: (Optional) Reason for the event (default: `"PHYSICAL_INTERACTION"`).
*   **Return Type**: `bool` (Whether the event was sent successfully).

### `sendOpenCloseEvent(String openDirection, int openPercent, String cause = FSTR_SINRICPRO_PHYSICAL_INTERACTION)`
*   **Purpose**: Sends an event to the SinricPro server to update the device's open/close status, including direction information.
*   **Parameters**:
    *   `openDirection`: Direction in which the device is opening (e.g., `"UP"`, `"DOWN"`).
    *   `openPercent`: Current open percentage (0-100).
    *   `cause`: (Optional) Reason for the event (default: `"PHYSICAL_INTERACTION"`).
*   **Return Type**: `bool` (Whether the event was sent successfully).

## Protected Methods

### `handleOpenCloseController(SinricProRequest &request)`
*   **Purpose**: Internal method used by the `OpenCloseController` to process incoming `SinricProRequest` objects. It delegates to the appropriate callback function based on the request type (`setOpenClose` or `adjustOpenClose`) and whether direction information is present.
*   **Parameters**:
    *   `request`: The incoming request to process.
*   **Return Type**: `bool` (Whether the request was handled successfully).

## Dependencies
*   `../SinricProRequest.h`
*   `../EventLimiter.h`
*   `../SinricProStrings.h`
*   `../SinricProNamespace.h`

## Tags
`SinricPro`, `SDK`, `Capability`, `OpenClose`, `Blinds`, `Curtains`, `Garage Door`, `Controller`, `Smart Home`, `ESP8266`, `ESP32`
