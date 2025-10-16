# Doorbell

## Overview
The `Doorbell` is a template class that provides the capability for SinricPro devices to report a doorbell press event. This controller is designed for devices like smart doorbells that need to notify the SinricPro server when their button is pressed.

## Template Parameters
*   `T`: The derived class that incorporates `Doorbell`. This allows the controller to interact with the derived device's properties and methods (e.g., `deviceId`, `prepareEvent`, `sendEvent`).

## Public Methods

### `Doorbell()`
*   **Purpose**: Constructor for the `Doorbell`. It initializes an `EventLimiter` to manage the rate of events sent.
*   **Parameters**: None
*   **Return Type**: None

### `sendDoorbellEvent(String cause = FSTR_SINRICPRO_PHYSICAL_INTERACTION)`
*   **Purpose**: Sends a `DoorbellPress` event to the SinricPro server, indicating that the doorbell button has been pressed.
*   **Parameters**:
    *   `cause`: (Optional) A `String` describing the reason why the event is sent (default is `"PHYSICAL_INTERACTION"`).
*   **Return Type**: `bool`
    *   `true`: The event has been sent successfully.
    *   `false`: The event has not been sent, possibly due to event rate limiting.
*   **Usage Example**:
    ```cpp
    // Assuming 'myDoorbell' is an instance of a device class inheriting Doorbell
    // When the doorbell button is pressed
    // myDoorbell.sendDoorbellEvent();
    ```

## Dependencies
*   `../EventLimiter.h`
*   `../SinricProStrings.h`
*   `../SinricProNamespace.h`

## Tags
`SinricPro`, `SDK`, `Capability`, `Doorbell`, `Event`, `Smart Home`, `ESP8266`, `ESP32`
