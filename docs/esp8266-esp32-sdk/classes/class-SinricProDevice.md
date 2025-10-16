# SinricProDevice

## Overview
The `SinricProDevice` class serves as the fundamental base class for all device types within the SinricPro SDK. It provides the essential functionalities and infrastructure required for any device to communicate with the `SinricProClass` and, consequently, the SinricPro server. This includes mechanisms for handling incoming requests, sending outgoing events, and managing core device properties like its unique ID and product type.

## Inheritance
`SinricProDevice` inherits from [`SinricProDeviceInterface`](./class-SinricProDeviceInterface.md).

## Constructor

### `SinricProDevice(const String &deviceId, const String &productType = "")`
*   **Purpose**: Constructs a new `SinricProDevice` object.
*   **Parameters**:
    *   `deviceId`: A `const String&` containing the unique identifier for this device.
    *   `productType`: A `const String&` specifying the type of product (e.g., `"SWITCH"`, `"LIGHT"`). Defaults to an empty string if not provided.
*   **Return Type**: None

## Public Methods

### `operator==(const String& other)`
*   **Purpose**: Overloads the equality operator (`==`) to allow comparison of a `SinricProDevice` object with a `String` (typically a device ID). It returns `true` if the device's ID matches the provided string.
*   **Parameters**:
    *   `other`: A `const String&` to compare against the device's ID.
*   **Return Type**: `bool`

### `getDeviceId()`
*   **Purpose**: Returns the unique identifier of the device.
*   **Parameters**: None
*   **Return Type**: `String`

## Protected Methods

### `~SinricProDevice()`
*   **Purpose**: Destructor for the `SinricProDevice` class. Handles cleanup when a device object is destroyed.
*   **Parameters**: None
*   **Return Type**: None

### `registerRequestHandler(const SinricProRequestHandler &requestHandler)`
*   **Purpose**: Registers a request handler function with the device. When a request is received for this device, it will be passed to the registered handlers until one successfully processes it.
*   **Parameters**:
    *   `requestHandler`: A `const SinricProRequestHandler&` (a function object or lambda) that can process incoming `SinricProRequest` objects.
*   **Return Type**: `void`

### `getTimestamp()`
*   **Purpose**: Retrieves the current timestamp (Unix epoch time) from the associated `SinricProInterface` (typically `SinricProClass`).
*   **Parameters**: None
*   **Return Type**: `unsigned long`

### `sign(const String& message)`
*   **Purpose**: Signs a given message using the signing mechanism provided by the associated `SinricProInterface`.
*   **Parameters**:
    *   `message`: A `const String&` containing the message to be signed.
*   **Return Type**: `String` (The signed message or signature string.)

### `sendEvent(JsonDocument &event)`
*   **Purpose**: Sends a `JsonDocument` (representing an event) to the SinricPro server via the associated `SinricProInterface`.
*   **Parameters**:
    *   `event`: A `JsonDocument&` containing the event data to be sent.
*   **Return Type**: `bool` (`true` if the event was successfully sent, `false` otherwise.)

### `prepareEvent(const char *action, const char *cause)`
*   **Purpose**: Prepares a `JsonDocument` that represents an event to be sent to the SinricPro server. It structures the event data with the device ID, action, and cause.
*   **Parameters**:
    *   `action`: A C-style string representing the action associated with the event (e.g., `"setPowerState"`).
    *   `cause`: A C-style string indicating the cause of the event (e.g., `"PHYSICAL_INTERACTION"`).
*   **Return Type**: `JsonDocument` (The prepared JSON event message.)

### `getProductType()`
*   **Purpose**: Returns the full product type string for the device (e.g., `"sinric.device.type.SWITCH"`).
*   **Parameters**: None
*   **Return Type**: `String`

### `begin(SinricProInterface *eventSender)`
*   **Purpose**: Initializes the device by providing it with a pointer to an `eventSender` (which is typically the `SinricProClass` instance). This link is crucial for the device to send events back to the SinricPro server.
*   **Parameters**:
    *   `eventSender`: A pointer to a `SinricProInterface` object.
*   **Return Type**: `void`

### `handleRequest(SinricProRequest &request)`
*   **Purpose**: Dispatches an incoming `SinricProRequest` to all registered request handlers. It iterates through the `requestHandlers` vector and calls each handler until one successfully processes the request.
*   **Parameters**:
    *   `request`: A `SinricProRequest&` object containing the details of the incoming request.
*   **Return Type**: `bool` (`true` if any handler successfully processed the request, `false` otherwise.)

## Private Members

### `deviceId`
*   **Purpose**: Stores the unique identifier of the device.
*   **Type**: `String`

### `requestHandlers`
*   **Purpose**: A vector storing function objects (or lambdas) that are capable of handling incoming `SinricProRequest` objects. These are typically registered by the capability controllers.
*   **Type**: `std::vector<SinricProRequestHandler>`

### `eventSender`
*   **Purpose**: A pointer to the `SinricProInterface` instance responsible for sending events and providing other core services (like timestamp and signing).
*   **Type**: `SinricProInterface*`

### `productType`
*   **Purpose**: Stores the base product type of the device (e.g., `"SWITCH"`, `"LIGHT"`).
*   **Type**: `String`

## Dependencies
*   `SinricProRequest.h`
*   `SinricProDeviceInterface.h`
*   `SinricProInterface.h`
*   `ArduinoJson.h` (indirectly via `SinricProInterface` and `SinricProRequest`)

## Tags
`SinricPro`, `SDK`, `Base Class`, `Device`, `Core`, `Request Handling`, `Event Sending`, `Smart Home`, `ESP8266`, `ESP32`
