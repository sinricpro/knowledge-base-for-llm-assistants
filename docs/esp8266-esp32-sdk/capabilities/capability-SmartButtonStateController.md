# SmartButtonStateController

## Overview
The `SmartButtonStateController` is a template class that provides functionality for SinricPro devices to respond to smart button press events initiated from the SinricPro App. This allows for virtual button presses (single, double, or long press) to trigger actions on the device, extending control beyond simple on/off states.

## Template Parameters
*   `T`: The device type that this controller is attached to. This allows the controller to interact with the derived device's properties and methods (e.g., `deviceId`).

## Enums

### `SmartButtonPressType`
*   **Purpose**: Defines the different types of button press events that can be recognized.
*   **Values**:
    *   `SINGLE_PRESS`: Represents a single, quick press of the button.
    *   `DOUBLE_PRESS`: Represents two quick presses of the button in succession.
    *   `LONG_PRESS`: Represents a sustained press of the button.

## Callback Handlers (Typedefs)

### `SmartButtonPressCallback`
*   **Purpose**: Defines the signature for the callback function that is invoked when the device receives a smart button press event from the SinricPro server.
*   **Signature**: `std::function<bool(const String& deviceId, SmartButtonPressType pressType)>`
*   **Parameters**:
    *   `deviceId`: A `String` containing the unique ID of the device.
    *   `pressType`: A `SmartButtonPressType` enum value indicating the type of button press (`SINGLE_PRESS`, `DOUBLE_PRESS`, or `LONG_PRESS`).
*   **Return Type**: `bool`
    *   `true`: The button press event was handled successfully.
    *   `false`: The button press event could not be handled properly.
*   **Usage Example**:
    ```cpp
    bool onSmartButtonPress(const String &deviceId, SmartButtonPressType pressType) {
      Serial.printf("Device %s received button press: ", deviceId.c_str());
      if (pressType == SmartButtonPressType::SINGLE_PRESS) Serial.println("SINGLE_PRESS");
      else if (pressType == SmartButtonPressType::DOUBLE_PRESS) Serial.println("DOUBLE_PRESS");
      else if (pressType == SmartButtonPressType::LONG_PRESS) Serial.println("LONG_PRESS");
      // Your code to perform action based on button press type
      return true;
    }
    // In setup:
    // myDevice.onButtonPress(onSmartButtonPress);
    ```

## Public Methods

### `SmartButtonStateController()`
*   **Purpose**: Constructor for the `SmartButtonStateController`. It registers a request handler with the device to process incoming smart button state change requests.
*   **Parameters**: None
*   **Return Type**: None

### `onButtonPress(SmartButtonPressCallback cb)`
*   **Purpose**: Registers the callback function that will be invoked when the SinricPro server sends a smart button press event to the device.
*   **Parameters**:
    *   `cb`: A `SmartButtonPressCallback` function pointer or lambda that will handle the button press event.
*   **Return Type**: `void`

## Protected Methods

### `handleSmartButtonStateController(SinricProRequest &request)`
*   **Purpose**: Internal method used by the `SmartButtonStateController` to process incoming `SinricProRequest` objects. It checks if the request is a `setSmartButtonState` action and, if so, converts the string state to `SmartButtonPressType` and invokes the registered `SmartButtonPressCallback`.
*   **Parameters**:
    *   `request`: The incoming request to process.
*   **Return Type**: `bool` (Indicates if the request was handled by this controller.)

## Private Methods

### `getSmartButtonPressType(const String& stateStr)`
*   **Purpose**: Converts a string representation of a button state (e.g., `"singlePress"`) into its corresponding `SmartButtonPressType` enum value.
*   **Parameters**:
    *   `stateStr`: The string representation of the button state.
*   **Return Type**: `SmartButtonPressType`

## Dependencies
*   `../SinricProRequest.h`
*   `../EventLimiter.h`
*   `../SinricProStrings.h`
*   `../SinricProNamespace.h`

## Tags
`SinricPro`, `SDK`, `Capability`, `Smart Button`, `Button`, `Controller`, `Event`, `Smart Home`, `ESP8266`, `ESP32`
