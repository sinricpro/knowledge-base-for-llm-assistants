# ContactSensor

## Overview
The `ContactSensor` is a template class that provides the capability for SinricPro devices to report the state of a contact sensor. This is typically used for devices like door/window sensors, indicating whether a contact is open or closed. This controller focuses on sending events to the SinricPro server to reflect the sensor's current status.

## Template Parameters
*   `T`: The derived class that incorporates `ContactSensor`. This allows the controller to interact with the derived device's properties and methods (e.g., `deviceId`, `prepareEvent`, `sendEvent`).

## Public Methods

### `ContactSensor()`
*   **Purpose**: Constructor for the `ContactSensor`. It initializes an `EventLimiter` to manage the rate of events sent.
*   **Parameters**: None
*   **Return Type**: None

### `sendContactEvent(bool detected, String cause = FSTR_SINRICPRO_PHYSICAL_INTERACTION)`
*   **Purpose**: Sends a `setContactState` event to the SinricPro server, informing it about the current state of the contact sensor.
*   **Parameters**:
    *   `detected`: A `bool` indicating the contact state. `true` typically means the contact is closed (e.g., door closed), and `false` means the contact is open (e.g., door open).
    *   `cause`: (Optional) A `String` describing the reason why the event is sent (default is `"PHYSICAL_INTERACTION"`).
*   **Return Type**: `bool`
    *   `true`: The event has been sent successfully.
    *   `false`: The event has not been sent, possibly due to event rate limiting (too many events sent in a short distance of time).
*   **Usage Example**:
    ```cpp
    // Assuming 'myContactSensor' is an instance of a device class inheriting ContactSensor
    // When the contact closes
    // myContactSensor.sendContactEvent(true);

    // When the contact opens
    // myContactSensor.sendContactEvent(false);
    ```

## Dependencies
*   `../EventLimiter.h`
*   `../SinricProStrings.h`
*   `../SinricProNamespace.h`

## Tags
`SinricPro`, `SDK`, `Capability`, `Contact Sensor`, `Sensor`, `Smart Home`, `ESP8266`, `ESP32`
