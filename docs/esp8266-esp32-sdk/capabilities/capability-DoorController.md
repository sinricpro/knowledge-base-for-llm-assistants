# DoorController

## Overview
The `DoorController` is a template class designed to provide control and reporting capabilities for door states (open/close). It is specifically intended for use with `GarageDoor` devices and is not meant to be a general-purpose capability for custom devices. It allows devices to receive commands to open or close a door and to send events reflecting the door's current state.

## Template Parameters
*   `T`: The derived class that incorporates `DoorController`. This allows the controller to interact with the derived device's properties and methods (e.g., `deviceId`, `prepareEvent`, `sendEvent`).

## Callback Handlers (Typedefs)

### `DoorCallback`
*   **Purpose**: Defines the signature for the callback function that is invoked when the device receives a request to open or close the door.
*   **Signature**: `std::function<bool(const String &deviceId, bool &doorState)>`
*   **Parameters**:
    *   `deviceId`: A `String` containing the unique ID of the device.
    *   `doorState`: A `bool` reference. On input, `false` indicates a request to open the door, and `true` indicates a request to close the door. The callback should update this parameter to reflect the actual state of the door after processing the request (`false` for open, `true` for closed).
*   **Return Type**: `bool`
    *   `true`: The request was handled successfully.
    *   `false`: The request could not be handled properly.
*   **Usage Example**:
    ```cpp
    bool onDoorState(const String &deviceId, bool &doorState) {
      Serial.printf("Device %s requested to %s door\r\n", deviceId.c_str(), doorState ? "close" : "open");
      // Your code to open/close the door
      // If successful, ensure 'doorState' reflects the actual state
      return true;
    }
    // In setup:
    // myGarageDoor.onDoorState(onDoorState);
    ```

## Public Methods

### `DoorController()`
*   **Purpose**: Constructor for the `DoorController`. It initializes an `EventLimiter` and registers a request handler with the derived device class to process door-related requests.
*   **Parameters**: None
*   **Return Type**: None

### `onDoorState(DoorCallback cb)`
*   **Purpose**: Sets the callback function that will be invoked when the SinricPro server sends a `setMode` command (for opening or closing the door) to the device.
*   **Parameters**:
    *   `cb`: A `DoorCallback` function pointer or lambda that will handle the door state change request.
*   **Return Type**: `void`

### `sendDoorStateEvent(bool state, String cause = FSTR_SINRICPRO_PHYSICAL_INTERACTION)`
*   **Purpose**: Sends a `setMode` event to the SinricPro server, informing it about the device's current door state. This is typically called when the door's state changes locally.
*   **Parameters**:
    *   `state`: A `bool` indicating the current door state (`true` for closed, `false` for open).
    *   `cause`: (Optional) A `String` describing the reason for the event (default is `"PHYSICAL_INTERACTION"`).
*   **Return Type**: `bool`
    *   `true`: The event was sent successfully.
    *   `false`: The event was not sent, possibly due to event rate limiting.
*   **Usage Example**:
    ```cpp
    // After opening the door locally
    // myGarageDoor.sendDoorStateEvent(false);

    // After closing the door locally
    // myGarageDoor.sendDoorStateEvent(true);
    ```

## Protected Methods

### `handleDoorController(SinricProRequest &request)`
*   **Purpose**: Internal method used by the `DoorController` to process incoming `SinricProRequest` objects. It checks if the request is a `setMode` action and, if so, invokes the registered `DoorCallback`.
*   **Parameters**:
    *   `request`: A reference to the `SinricProRequest` object received from the server.
*   **Return Type**: `bool` (Indicates if the request was handled by this controller.)

## Dependencies
*   `../SinricProRequest.h`
*   `../EventLimiter.h`
*   `../SinricProStrings.h`
*   `../SinricProNamespace.h`

## Tags
`SinricPro`, `SDK`, `Capability`, `Door`, `Garage Door`, `Controller`, `Smart Home`, `ESP8266`, `ESP32`
