# StartStopController

## Overview
The `StartStopController` is a template class that provides functionality for SinricPro devices that can be started, stopped, paused, and unpaused. This is commonly used for appliances like vacuum cleaners, washing machines, dishwashers, or other devices with distinct operational cycles. It allows for remote control of these states and reporting of the device's current status.

## Template Parameters
*   `T`: The device type that this controller is attached to. This allows the controller to interact with the derived device's properties and methods (e.g., `deviceId`, `prepareEvent`, `sendEvent`).

## Callback Handlers (Typedefs)

### `StartStopCallback`
*   **Purpose**: Defines the signature for the callback function that is invoked when the device receives a `setStartStop` request from the SinricPro server.
*   **Signature**: `std::function<bool(const String &deviceId, bool &start)>`
*   **Parameters**:
    *   `deviceId`: A `String` containing the unique ID of the device.
    *   `start`: A `bool` reference. On input, `true` indicates a request to start the device, and `false` indicates a request to stop. The callback should update this parameter to reflect the actual state of the device after processing the request (`true` if started, `false` if stopped).
*   **Return Type**: `bool`
    *   `true`: The request was handled successfully.
    *   `false`: The request could not be handled properly.
*   **Usage Example**:
    ```cpp
    bool onStartStop(const String &deviceId, bool &start) {
      Serial.printf("Device %s requested to %s\r\n", deviceId.c_str(), start ? "start" : "stop");
      // Your code to start/stop the device
      return true;
    }
    // In setup:
    // myVacuum.onStartStop(onStartStop);
    ```

### `PauseUnpauseCallback`
*   **Purpose**: Defines the signature for the callback function that is invoked when the device receives a `setPauseUnpause` request from the SinricPro server.
*   **Signature**: `std::function<bool(const String &deviceId, bool &pause)>`
*   **Parameters**:
    *   `deviceId`: A `String` containing the unique ID of the device.
    *   `pause`: A `bool` reference. On input, `true` indicates a request to pause the device, and `false` indicates a request to unpause. The callback should update this parameter to reflect the actual state of the device after processing the request (`true` if paused, `false` if unpaused).
*   **Return Type**: `bool`
    *   `true`: The request was handled successfully.
    *   `false`: The request could not be handled properly.
*   **Usage Example**:
    ```cpp
    bool onPauseUnpause(const String &deviceId, bool &pause) {
      Serial.printf("Device %s requested to %s\r\n", deviceId.c_str(), pause ? "pause" : "unpause");
      // Your code to pause/unpause the device
      return true;
    }
    // In setup:
    // myWashingMachine.onPauseUnpause(onPauseUnpause);
    ```

## Public Methods

### `StartStopController()`
*   **Purpose**: Constructor for the `StartStopController`. It initializes an `EventLimiter` and registers a request handler with the derived device class to process start/stop and pause/unpause requests.
*   **Parameters**: None
*   **Return Type**: None

### `onStartStop(StartStopCallback cb)`
*   **Purpose**: Sets the callback function that will be invoked when the SinricPro server sends a `setStartStop` command to the device.
*   **Parameters**:
    *   `cb`: A `StartStopCallback` function pointer or lambda that will handle the start/stop request.
*   **Return Type**: `void`

### `onPauseUnpause(PauseUnpauseCallback cb)`
*   **Purpose**: Sets the callback function that will be invoked when the SinricPro server sends a `setPauseUnpause` command to the device.
*   **Parameters**:
    *   `cb`: A `PauseUnpauseCallback` function pointer or lambda that will handle the pause/unpause request.
*   **Return Type**: `void`

### `sendStartStopEvent(bool start, String cause = FSTR_SINRICPRO_PHYSICAL_INTERACTION)`
*   **Purpose**: Sends a `setStartStop` event to the SinricPro server, informing it about the device's current start/stop state. This is typically called when the device's state changes locally.
*   **Parameters**:
    *   `start`: A `bool` indicating the current state (`true` for started, `false` for stopped).
    *   `cause`: (Optional) A `String` describing the reason for the event (default is `"PHYSICAL_INTERACTION"`).
*   **Return Type**: `bool`
    *   `true`: The event was sent successfully.
    *   `false`: The event was not sent, possibly due to event rate limiting.
*   **Usage Example**:
    ```cpp
    // After starting the device locally
    // myVacuum.sendStartStopEvent(true);
    ```

### `sendPauseUnpauseEvent(bool pause, String cause = FSTR_SINRICPRO_PHYSICAL_INTERACTION)`
*   **Purpose**: Sends a `setPauseUnpause` event to the SinricPro server, informing it about the device's current pause/unpause state. This is typically called when the device's state changes locally.
*   **Parameters**:
    *   `pause`: A `bool` indicating the current state (`true` for paused, `false` for unpaused).
    *   `cause`: (Optional) A `String` describing the reason for the event (default is `"PHYSICAL_INTERACTION"`).
*   **Return Type**: `bool`
    *   `true`: The event was sent successfully.
    *   `false`: The event was not sent, possibly due to event rate limiting.
*   **Usage Example**:
    ```cpp
    // After pausing the device locally
    // myWashingMachine.sendPauseUnpauseEvent(true);
    ```

## Protected Methods

### `handleStartStopController(SinricProRequest &request)`
*   **Purpose**: Internal method used by the `StartStopController` to process incoming `SinricProRequest` objects. It checks if the request is a `setStartStop` or `setPauseUnpause` action and, if so, invokes the corresponding registered callback.
*   **Parameters**:
    *   `request`: A reference to the `SinricProRequest` object received from the server.
*   **Return Type**: `bool` (Indicates if the request was handled by this controller.)

## Dependencies
*   `../SinricProRequest.h`
*   `../EventLimiter.h`
*   `../SinricProStrings.h`
*   `../SinricProNamespace.h`

## Tags
`SinricPro`, `SDK`, `Capability`, `StartStop`, `PauseUnpause`, `Controller`, `Smart Home`, `Appliance`, `ESP8266`, `ESP32`
