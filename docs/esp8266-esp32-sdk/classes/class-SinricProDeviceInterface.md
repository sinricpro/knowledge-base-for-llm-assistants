# SinricProDeviceInterface

## Overview
`SinricProDeviceInterface` is an abstract base class (interface) that defines the fundamental contract for all SinricPro devices. Any class representing a smart home device in the SinricPro ecosystem must inherit from this interface and implement its pure virtual methods. It ensures a consistent way for the `SinricProClass` to interact with various device types.

## Inheritance
None (This is a base interface).

## Protected Virtual Methods

### `handleRequest(SinricProRequest& request)`
*   **Purpose**: This pure virtual method is responsible for handling incoming requests from the SinricPro server that are directed to this specific device. Device implementations must define how to process these requests.
*   **Parameters**:
    *   `request`: A reference to a `SinricProRequest` object containing the details of the incoming request (action, payload, etc.).
*   **Return Type**: `bool` (Returns `true` if the request was handled successfully, `false` otherwise.)

### `getDeviceId()`
*   **Purpose**: This pure virtual method returns the unique identifier (ID) of the device.
*   **Parameters**: None
*   **Return Type**: `String` (The unique device ID.)

### `getProductType()`
*   **Purpose**: This pure virtual method returns the product type of the device (e.g., "LIGHT", "SWITCH", "THERMOSTAT").
*   **Parameters**: None
*   **Return Type**: `String` (The product type string.)

### `begin(SinricProInterface* eventSender)`
*   **Purpose**: This pure virtual method initializes the device, typically by providing it with a pointer to an `eventSender` (which is usually the `SinricProClass` instance). This allows the device to send events back to the SinricPro server.
*   **Parameters**:
    *   `eventSender`: A pointer to a `SinricProInterface` object, used for sending events.
*   **Return Type**: `void`

### `getTimestamp()`
*   **Purpose**: This pure virtual method returns the current timestamp (Unix epoch time) as known by the SinricPro system. This is often used for timestamping events or requests.
*   **Parameters**: None
*   **Return Type**: `unsigned long` (The current timestamp.)

## Dependencies
*   `SinricProInterface.h`
*   `SinricProNamespace.h`
*   `SinricProRequest.h`

## Tags
`SinricPro`, `SDK`, `Interface`, `Base Class`, `Device`, `Abstract`, `ESP8266`, `ESP32`
