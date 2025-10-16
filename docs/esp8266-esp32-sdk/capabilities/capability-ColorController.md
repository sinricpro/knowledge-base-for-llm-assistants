# ColorController

## Overview
The `ColorController` is a template class that enables SinricPro devices to manage and report their color using RGB (Red, Green, Blue) values. Devices integrating this controller can receive `setColor` commands from the SinricPro server and send `setColor` events to update their color status.

## Template Parameters
*   `T`: The derived class that incorporates `ColorController`. This allows the controller to access members of the derived device class (e.g., `deviceId`, `prepareEvent`, `sendEvent`).

## Callback Handlers (Typedefs)

### `ColorCallback`
*   **Purpose**: Defines the signature for the callback function that is invoked when the device receives a `setColor` request from the SinricPro server.
*   **Signature**: `std::function<bool(const String &deviceId, byte &r, byte &g, byte &b)>`
*   **Parameters**:
    *   `deviceId`: A `String` containing the unique ID of the device.
    *   `r`: A `byte` reference for the Red component (0-255). On input, it's the requested value; on output, it should be the actual value set.
    *   `g`: A `byte` reference for the Green component (0-255). On input, it's the requested value; on output, it should be the actual value set.
    *   `b`: A `byte` reference for the Blue component (0-255). On input, it's the requested value; on output, it should be the actual value set.
*   **Return Type**: `bool`
    *   `true`: The request was handled successfully.
    *   `false`: The request could not be handled properly.
*   **Usage Example**:
    ```cpp
    bool onColor(const String &deviceId, byte &r, byte &g, byte &b) {
      Serial.printf("Device %s set color to R:%d G:%d B:%d\r\n", deviceId.c_str(), r, g, b);
      // Your code to set the device color
      // If successful, ensure r, g, b reflect the actual state
      return true;
    }
    // In setup:
    // myLight.onColor(onColor);
    ```

## Public Methods

### `ColorController()`
*   **Purpose**: Constructor for the `ColorController`. It initializes an `EventLimiter` and registers a request handler with the derived device class to process color-related requests.
*   **Parameters**: None
*   **Return Type**: None

### `onColor(ColorCallback cb)`
*   **Purpose**: Sets the callback function that will be invoked when the SinricPro server sends a `setColor` command to the device.
*   **Parameters**:
    *   `cb`: A `ColorCallback` function pointer or lambda that will handle the color setting request.
*   **Return Type**: `void`

### `sendColorEvent(byte r, byte g, byte b, String cause = FSTR_SINRICPRO_PHYSICAL_INTERACTION)`
*   **Purpose**: Sends a `setColor` event to the SinricPro server, informing it about the device's current RGB color. This is typically called when the device's color changes locally.
*   **Parameters**:
    *   `r`: A `byte` indicating the current Red component (0-255).
    *   `g`: A `byte` indicating the current Green component (0-255).
    *   `b`: A `byte` indicating the current Blue component (0-255).
    *   `cause`: (Optional) A `String` describing the reason for the event (default is `"PHYSICAL_INTERACTION"`).
*   **Return Type**: `bool`
    *   `true`: The event was sent successfully.
    *   `false`: The event was not sent, possibly due to event rate limiting.
*   **Usage Example**:
    ```cpp
    // After setting color locally
    // myLight.sendColorEvent(255, 0, 0); // Red
    ```

## Protected Methods

### `handleColorController(SinricProRequest &request)`
*   **Purpose**: Internal method used by the `ColorController` to process incoming `SinricProRequest` objects. It checks if the request is a `setColor` action and, if so, invokes the registered `ColorCallback`.
*   **Parameters**:
    *   `request`: A reference to the `SinricProRequest` object received from the server.
*   **Return Type**: `bool` (Indicates if the request was handled by this controller.)

## Dependencies
*   `../SinricProRequest.h`
*   `../EventLimiter.h`
*   `../SinricProStrings.h`
*   `../SinricProNamespace.h`

## Tags
`SinricPro`, `SDK`, `Capability`, `Color`, `RGB`, `Controller`, `Smart Home`, `Light`, `ESP8266`, `ESP32`
