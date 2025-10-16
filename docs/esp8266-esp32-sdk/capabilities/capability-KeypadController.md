# KeypadController

## Overview
The `KeypadController` is a template class that provides functionality for SinricPro devices to receive and process virtual keystroke commands. This capability is typically used for devices that can be controlled like a remote, such as smart TVs, media players, or set-top boxes, allowing users to send commands like navigation (UP, DOWN, LEFT, RIGHT), selection (SELECT), or information (INFO).

## Template Parameters
*   `T`: The derived class that incorporates `KeypadController`. This allows the controller to interact with the derived device's properties and methods (e.g., `deviceId`).

## Callback Handlers (Typedefs)

### `KeystrokeCallback`
*   **Purpose**: Defines the signature for the callback function that is invoked when the device receives a `sendKeystroke` request from the SinricPro server.
*   **Signature**: `std::function<bool(const String &deviceId, String &keystroke)>`
*   **Parameters**:
    *   `deviceId`: A `String` containing the unique ID of the device.
    *   `keystroke`: A `String` reference. On input, it represents the requested keystroke (e.g., `"UP"`, `"SELECT"`, `"INFO"`, `"PAGE_UP"`). The callback should update this parameter to reflect the actual keystroke processed by the device.
*   **Return Type**: `bool`
    *   `true`: The request was handled successfully.
    *   `false`: The request could not be handled properly.
*   **Usage Example**:
    ```cpp
    bool onKeystroke(const String &deviceId, String &keystroke) {
      Serial.printf("Device %s received keystroke: %s\r\n", deviceId.c_str(), keystroke.c_str());
      // Your code to process the keystroke
      return true;
    }
    // In setup:
    // myTV.onKeystroke(onKeystroke);
    ```

## Public Methods

### `KeypadController()`
*   **Purpose**: Constructor for the `KeypadController`. It registers a request handler with the derived device class to process keystroke requests.
*   **Parameters**: None
*   **Return Type**: None

### `onKeystroke(KeystrokeCallback cb)`
*   **Purpose**: Sets the callback function that will be invoked when the SinricPro server sends a `sendKeystroke` command to the device.
*   **Parameters**:
    *   `cb`: A `KeystrokeCallback` function pointer or lambda that will handle the keystroke request.
*   **Return Type**: `void`

## Protected Methods

### `handleKeypadController(SinricProRequest &request)`
*   **Purpose**: Internal method used by the `KeypadController` to process incoming `SinricProRequest` objects. It checks if the request is a `sendKeystroke` action and, if so, invokes the registered `KeystrokeCallback`.
*   **Parameters**:
    *   `request`: A reference to the `SinricProRequest` object received from the server.
*   **Return Type**: `bool` (Indicates if the request was handled by this controller.)

## Dependencies
*   `../SinricProRequest.h`
*   `../SinricProStrings.h`
*   `../SinricProNamespace.h`

## Tags
`SinricPro`, `SDK`, `Capability`, `Keypad`, `Remote Control`, `Controller`, `Smart Home`, `TV`, `Media Player`, `ESP8266`, `ESP32`
