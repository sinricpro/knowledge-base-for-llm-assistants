# LockController

## Overview
The `LockController` is a template class that provides functionality for SinricPro devices to control and report their lock state. This is typically used for smart locks, allowing them to be locked or unlocked remotely and to report their current status, including a 'JAMMED' state for error reporting.

## Template Parameters
*   `T`: The derived class that incorporates `LockController`. This allows the controller to interact with the derived device's properties and methods (e.g., `deviceId`, `prepareEvent`, `sendEvent`).

## Callback Handlers (Typedefs)

### `LockStateCallback`
*   **Purpose**: Defines the signature for the callback function that is invoked when the device receives a `setLockState` request from the SinricPro server.
*   **Signature**: `std::function<bool(const String &deviceId, bool &state)>`
*   **Parameters**:
    *   `deviceId`: A `String` containing the unique ID of the device.
    *   `state`: A `bool` reference. On input, `true` indicates a request to lock the device, and `false` indicates a request to unlock. The callback should update this parameter to reflect the actual lock state of the device after processing the request (`true` for locked, `false` for unlocked).
*   **Return Type**: `bool`
    *   `true`: The request was handled successfully.
    *   `false`: The request could not be handled properly (e.g., device jammed).
*   **Usage Example**:
    ```cpp
    bool onLockState(const String &deviceId, bool &lockState) {
      Serial.printf("Device %s requested to be %s\r\n", deviceId.c_str(), lockState ? "locked" : "unlocked");
      // Your code to lock/unlock the device
      // If successful, ensure 'lockState' reflects the actual state
      return true;
    }
    // In setup:
    // myLock.onLockState(onLockState);
    ```

## Public Methods

### `LockController()`
*   **Purpose**: Constructor for the `LockController`. It initializes an `EventLimiter` and registers a request handler with the derived device class to process lock-related requests.
*   **Parameters**: None
*   **Return Type**: None

### `onLockState(LockStateCallback cb)`
*   **Purpose**: Sets the callback function that will be invoked when the SinricPro server sends a `setLockState` command to the device.
*   **Parameters**:
    *   `cb`: A `LockStateCallback` function pointer or lambda that will handle the lock state change request.
*   **Return Type**: `void`

### `sendLockStateEvent(bool state, String cause = FSTR_SINRICPRO_PHYSICAL_INTERACTION)`
*   **Purpose**: Sends a `setLockState` event to the SinricPro server, informing it about the device's current lock state. This is typically called when the device's lock state changes locally.
*   **Parameters**:
    *   `state`: A `bool` indicating the current lock state (`true` for locked, `false` for unlocked).
    *   `cause`: (Optional) A `String` describing the reason for the event (default is `"PHYSICAL_INTERACTION"`).
*   **Return Type**: `bool`
    *   `true`: The event was sent successfully.
    *   `false`: The event was not sent, possibly due to event rate limiting.
*   **Usage Example**:
    ```cpp
    // After locking the device locally
    // myLock.sendLockStateEvent(true);

    // After unlocking the device locally
    // myLock.sendLockStateEvent(false);
    ```

## Protected Methods

### `handleLockController(SinricProRequest &request)`
*   **Purpose**: Internal method used by the `LockController` to process incoming `SinricProRequest` objects. It checks if the request is a `setLockState` action and, if so, invokes the registered `LockStateCallback`.
*   **Parameters**:
    *   `request`: A reference to the `SinricProRequest` object received from the server.
*   **Return Type**: `bool` (Indicates if the request was handled by this controller.)

## Dependencies
*   `../SinricProRequest.h`
*   `../EventLimiter.h`
*   `../SinricProStrings.h`
*   `../SinricProNamespace.h`

## Tags
`SinricPro`, `SDK`, `Capability`, `Lock`, `Security`, `Controller`, `Smart Home`, `ESP8266`, `ESP32`
