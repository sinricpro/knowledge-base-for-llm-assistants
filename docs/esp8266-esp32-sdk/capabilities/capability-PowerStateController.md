# PowerStateController

## Overview
The `PowerStateController` is a template class that provides the necessary functionality for a SinricPro device to manage and report its power state (ON/OFF). Devices that include this controller can receive `setPowerState` commands from the SinricPro server and send `setPowerState` events to reflect their current status. It is designed to be inherited by device classes, allowing them to easily integrate power state control.

## Template Parameters
*   `T`: The derived class that inherits from `PowerStateController`. This pattern (CRTP - Curiously Recurring Template Pattern) allows the controller to access members of the derived device class (e.g., `deviceId`, `prepareEvent`, `sendEvent`).

## Callback Handlers (Typedefs)

### `PowerStateCallback`
*   **Purpose**: Defines the signature for the callback function that is invoked when the device receives a `setPowerState` request from the SinricPro server.
*   **Signature**: `std::function<bool(const String &deviceId, bool &state)>`
*   **Parameters**:
    *   `deviceId`: A `String` containing the unique ID of the device for which the power state change is requested.
    *   `state`: A `bool` reference. On input, it indicates the requested power state (`true` for ON, `false` for OFF). The callback should update this parameter to reflect the actual power state of the device after processing the request.
*   **Return Type**: `bool`
    *   `true`: The request was handled successfully, and the device's power state was updated (or will be updated) as requested.
    *   `false`: The request could not be handled properly due to an error or other reason.
*   **Usage Example**:
    ```cpp
    bool onPowerState(const String &deviceId, bool &state) {
      Serial.printf("Device %s turned %s\r\n", deviceId.c_str(), state ? "ON" : "OFF");
      // Your code to turn the device ON/OFF
      // If successful, ensure 'state' reflects the actual state
      return true; // Indicate success
    }
    // In setup:
    // SinricProSwitch &mySwitch = SinricPro[SWITCH_ID];
    // mySwitch.onPowerState(onPowerState);
    ```

## Public Methods

### `PowerStateController()`
*   **Purpose**: Constructor for the `PowerStateController`. It initializes an `EventLimiter` and registers a request handler with the derived device class to process `setPowerState` requests.
*   **Parameters**: None
*   **Return Type**: None

### `onPowerState(PowerStateCallback cb)`
*   **Purpose**: Sets the callback function that will be executed when the SinricPro server sends a `setPowerState` command to the device.
*   **Parameters**:
    *   `cb`: A `PowerStateCallback` function pointer or lambda that will handle the power state change request.
*   **Return Type**: `void`

### `sendPowerStateEvent(bool state, String cause = FSTR_SINRICPRO_PHYSICAL_INTERACTION)`
*   **Purpose**: Sends a `setPowerState` event to the SinricPro server, informing it about the device's current power state. This is typically called when the device's power state changes locally (e.g., via a physical button).
*   **Parameters**:
    *   `state`: A `bool` indicating the current power state (`true` for ON, `false` for OFF).
    *   `cause`: (Optional) A `String` describing the reason for the event (default is `"PHYSICAL_INTERACTION"`). Other common causes include `"APP_INTERACTION"`.
*   **Return Type**: `bool`
    *   `true`: The event was sent successfully.
    *   `false`: The event was not sent, possibly due to event rate limiting (too many events sent in a short period).
*   **Usage Example**:
    ```cpp
    // After turning device ON locally
    // mySwitch.sendPowerStateEvent(true);
    ```

## Protected Methods

### `handlePowerStateController(SinricProRequest &request)`
*   **Purpose**: Internal method used by the `PowerStateController` to process incoming `SinricProRequest` objects. It checks if the request is a `setPowerState` action and, if so, invokes the registered `PowerStateCallback`.
*   **Parameters**:
    *   `request`: A reference to the `SinricProRequest` object received from the server.
*   **Return Type**: `bool` (Indicates if the request was handled by this controller.)

## Dependencies
*   `../SinricProRequest.h`
*   `../EventLimiter.h`
*   `../SinricProStrings.h`
*   `../SinricProNamespace.h`

## Tags
`SinricPro`, `SDK`, `Capability`, `PowerState`, `ON/OFF`, `Controller`, `Smart Home`, `ESP8266`, `ESP32`
