# InputController

## Overview
The `InputController` is a template class that provides functionality for SinricPro devices to manage and report their active input source. This capability is commonly used for multimedia devices such as TVs, soundbars, or receivers that can switch between various audio and video inputs (e.g., HDMI, AUX, USB).

## Template Parameters
* `T`: The derived class that incorporates `InputController`. This allows the controller to interact with the derived device's properties and methods (e.g., `deviceId`, `prepareEvent`, `sendEvent`).

## Callback Handlers (Typedefs)

### `SelectInputCallback`
* **Purpose**: Defines the signature for the callback function that is invoked when the device receives a `selectInput` request from the SinricPro server.
* **Signature**: `std::function<bool(const String &deviceId, String &input)>`
* **Parameters**:
    * `deviceId`: A `String` containing the unique ID of the device.
    * `input`: A `String` reference. On input, it represents the requested input name (e.g., "HDMI 1", "AUX 1", "TV"). The callback should update this parameter to reflect the actual input name that the device has switched to.
* **Return Type**: `bool`
    * `true`: The request was handled successfully.
    * `false`: The request could not be handled properly.
* **Usage Example**:
    ```cpp
    bool onSelectInput(const String &deviceId, String &input) {
      Serial.printf("Device %s requested to switch to input: %s\r\n", deviceId.c_str(), input.c_str());
      // Your code to switch the device input
      // If successful, ensure 'input' reflects the actual input
      return true;
    }
    // In setup:
    // myTV.onSelectInput(onSelectInput);
    ```

## Public Methods

### `InputController()`
* **Purpose**: Constructor for the `InputController`. It initializes an `EventLimiter` and registers a request handler with the derived device class to process input-related requests.
* **Parameters**: None
* **Return Type**: None

### `onSelectInput(SelectInputCallback cb)`
* **Purpose**: Sets the callback function that will be invoked when the SinricPro server sends a `selectInput` command to the device.
* **Parameters**:
    * `cb`: A `SelectInputCallback` function pointer or lambda that will handle the input selection request.
* **Return Type**: `void`

### `sendSelectInputEvent(String input, String cause = FSTR_SINRICPRO_PHYSICAL_INTERACTION)`
* **Purpose**: Sends a `selectInput` event to the SinricPro server, informing it about the device's currently active input. This is typically called when the device's input changes locally.
* **Parameters**:
    * `input`: A `String` indicating the current active input (e.g., "HDMI 1").
    * `cause`: (Optional) A `String` describing the reason for the event (default is "PHYSICAL_INTERACTION").
* **Return Type**: `bool`
    * `true`: The event was sent successfully.
    * `false`: The event was not sent, possibly due to event rate limiting.
* **Usage Example**:
    ```cpp
    // After switching input locally
    // myTV.sendSelectInputEvent("HDMI 2");
    ```

## Protected Methods

### `handleInputController(SinricProRequest &request)`
* **Purpose**: Internal method used by the `InputController` to process incoming `SinricProRequest` objects. It checks if the request is a `selectInput` action and, if so, invokes the registered `SelectInputCallback`.
* **Parameters**:
    * `request`: A reference to the `SinricProRequest` object received from the server.
* **Return Type**: `bool` (Indicates if the request was handled by this controller.)

## Dependencies
* `../SinricProRequest.h`
* `../EventLimiter.h`
* `../SinricProStrings.h`
* `../SinricProNamespace.h`

## Tags
`SinricPro`, `SDK`, `Capability`, `Input`, `Controller`, `Smart Home`, `TV`, `Audio`, `ESP8266`, `ESP32`
