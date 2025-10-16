# ToggleController

## Overview
The `ToggleController` is a template class that provides functionality for SinricPro devices to manage and respond to toggleable states for specific instances or features. This is useful for devices that have multiple independent on/off switches or settings, allowing fine-grained control over each component.

## Template Parameters
*   `T`: The device type that this controller is attached to. This allows the controller to interact with the derived device's properties and methods (e.g., `deviceId`, `prepareEvent`, `sendEvent`).

## Callback Handlers (Typedefs)

### `GenericToggleStateCallback`
*   **Purpose**: Defines the signature for the callback function that is invoked when the device receives a `setToggleState` request for a specific instance from the SinricPro server.
*   **Signature**: `std::function<bool(const String &deviceId, const String& instance, bool &state)>`
*   **Parameters**:
    *   `deviceId`: A `String` containing the unique ID of the device.
    *   `instance`: A `String` representing the specific instance (e.g., `"Light1"`, `"Fan2"`) for which the toggle state is being set.
    *   `state`: A `bool` reference. On input, `true` indicates a request to turn the instance ON, and `false` indicates a request to turn it OFF. The callback should update this parameter to reflect the actual state of the instance after processing the request (`true` for ON, `false` for OFF).
*   **Return Type**: `bool`
    *   `true`: The request was handled successfully.
    *   `false`: The request could not be handled properly.
*   **Usage Example**:
    ```cpp
    bool onToggleState(const String &deviceId, const String &instance, bool &state) {
      Serial.printf("Device %s instance %s requested to be %s\r\n", deviceId.c_str(), instance.c_str(), state ? "ON" : "OFF");
      // Your code to toggle the specific instance
      return true;
    }
    // In setup:
    // myMultiSwitch.onToggleState("Light1", onToggleState);
    ```

## Public Methods

### `ToggleController()`
*   **Purpose**: Constructor for the `ToggleController`. It registers a request handler with the derived device class to process toggle-related requests.
*   **Parameters**: None
*   **Return Type**: None

### `onToggleState(const String& instance, GenericToggleStateCallback cb)`
*   **Purpose**: Sets the callback function that will be invoked when the SinricPro server sends a `setToggleState` command for a specific instance to the device.
*   **Parameters**:
    *   `instance`: A `String` representing the instance name.
    *   `cb`: A `GenericToggleStateCallback` function pointer or lambda that will handle the toggle state change request for the specified instance.
*   **Return Type**: `void`

### `sendToggleStateEvent(const String &instance, bool state, String cause = FSTR_SINRICPRO_PHYSICAL_INTERACTION)`
*   **Purpose**: Sends a `setToggleState` event to the SinricPro server, informing it about the current toggle state of a specific instance. This is typically called when the instance's state changes locally.
*   **Parameters**:
    *   `instance`: A `String` representing the instance name.
    *   `state`: A `bool` indicating the current state (`true` for ON, `false` for OFF).
    *   `cause`: (Optional) A `String` describing the reason for the event (default is `"PHYSICAL_INTERACTION"`).
*   **Return Type**: `bool`
    *   `true`: The event was sent successfully.
    *   `false`: The event was not sent, possibly due to event rate limiting.
*   **Usage Example**:
    ```cpp
    // After toggling instance 'Light1' locally
    // myMultiSwitch.sendToggleStateEvent("Light1", true);
    ```

## Protected Methods

### `handleToggleController(SinricProRequest &request)`
*   **Purpose**: Internal method used by the `ToggleController` to process incoming `SinricProRequest` objects. It checks if the request is a `setToggleState` action for a known instance and, if so, invokes the corresponding registered callback.
*   **Parameters**:
    *   `request`: A reference to the `SinricProRequest` object received from the server.
*   **Return Type**: `bool` (Indicates if the request was handled by this controller.)

## Dependencies
*   `../SinricProRequest.h`
*   `../EventLimiter.h`
*   `../SinricProStrings.h`
*   `../SinricProNamespace.h`

## Tags
`SinricPro`, `SDK`, `Capability`, `Toggle`, `Switch`, `Controller`, `Smart Home`, `Multi-instance`, `ESP8266`, `ESP32`
