# MotionSensor

## Overview
The `MotionSensor` is a template class that provides functionality for SinricPro devices to report motion detection events. This is typically used for PIR sensors or other motion-sensing devices to notify the SinricPro server when motion is detected or no longer detected.

## Template Parameters
*   `T`: The derived class that incorporates `MotionSensor`. This allows the controller to interact with the derived device's properties and methods (e.g., `deviceId`, `prepareEvent`, `sendEvent`).

## Public Methods

### `MotionSensor()`
*   **Purpose**: Constructor for the `MotionSensor`. It initializes an `EventLimiter` to manage the rate of events sent.
*   **Parameters**: None
*   **Return Type**: None

### `sendMotionEvent(bool detected, String cause = FSTR_SINRICPRO_PHYSICAL_INTERACTION)`
*   **Purpose**: Sends a `motion` event to the SinricPro server, informing it about the current motion detection state.
*   **Parameters**:
    *   `detected`: A `bool` indicating the motion state. `true` means motion has been detected, and `false` means no motion has been detected.
    *   `cause`: (Optional) A `String` describing the reason why the event is sent (default is `"PHYSICAL_INTERACTION"`).
*   **Return Type**: `bool`
    *   `true`: The event has been sent successfully.
    *   `false`: The event has not been sent, possibly due to event rate limiting.
*   **Usage Example**:
    ```cpp
    // Assuming 'myMotionSensor' is an instance of a device class inheriting MotionSensor
    // When motion is detected
    // myMotionSensor.sendMotionEvent(true);

    // When motion is no longer detected
    // myMotionSensor.sendMotionEvent(false);
    ```

## Dependencies
*   `../EventLimiter.h`
*   `../SinricProStrings.h`
*   `../SinricProNamespace.h`

## Tags
`SinricPro`, `SDK`, `Capability`, `Motion Sensor`, `Sensor`, `Smart Home`, `Security`, `ESP8266`, `ESP32`
