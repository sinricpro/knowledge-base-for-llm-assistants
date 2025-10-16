# SinricProInterface

## Overview
`SinricProInterface` is an abstract base class (interface) that outlines the essential functionalities required for any component that needs to interact with the SinricPro server by sending messages or events. It also provides methods to query connection status and retrieve timestamps. The `SinricProClass` implements this interface.

## Inheritance
None (This is a base interface).

## Protected Virtual Methods

### `sendMessage(JsonDocument& jsonEvent)`
*   **Purpose**: This pure virtual method is responsible for sending a `JsonDocument` (representing an event or message) to the SinricPro server. Implementations will handle the actual transmission over the network.
*   **Parameters**:
    *   `jsonEvent`: A reference to a `JsonDocument` containing the message or event data to be sent.
*   **Return Type**: `void`

### `sign(const String& message)`
*   **Purpose**: This pure virtual method is used to sign a given string message, typically for authentication and integrity verification with the SinricPro server. The signing mechanism (e.g., HMAC) is left to the implementing class.
*   **Parameters**:
    *   `message`: The `String` message to be signed.
*   **Return Type**: `String` (The signed message or signature string.)

### `prepareEvent(String deviceId, const char* action, const char* cause)`
*   **Purpose**: This pure virtual method prepares a `JsonDocument` that represents an event to be sent to the SinricPro server. It structures the event data with the device ID, action, and cause.
*   **Parameters**:
    *   `deviceId`: The unique ID of the device from which the event originates.
    *   `action`: A C-style string representing the action associated with the event (e.g., "setPowerState").
    *   `cause`: A C-style string indicating the cause of the event (e.g., "PHYSICAL_INTERACTION").
*   **Return Type**: `JsonDocument` (The prepared JSON event message.)

### `getTimestamp()`
*   **Purpose**: This pure virtual method retrieves the current timestamp (Unix epoch time) as maintained by the SinricPro system. This timestamp is crucial for various operations, including message creation and synchronization.
*   **Parameters**: None
*   **Return Type**: `unsigned long` (The current timestamp.)

### `isConnected()`
*   **Purpose**: This pure virtual method checks and returns the current connection status to the SinricPro server.
*   **Parameters**: None
*   **Return Type**: `bool` (Returns `true` if connected, `false` otherwise.)

## Dependencies
*   `ArduinoJson.h`
*   `SinricProNamespace.h`
*   `SinricProQueue.h`

## Tags
`SinricPro`, `SDK`, `Interface`, `Communication`, `Abstract`, `ESP8266`, `ESP32`
