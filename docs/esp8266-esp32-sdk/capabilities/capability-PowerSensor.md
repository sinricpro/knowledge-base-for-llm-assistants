# PowerSensor

## Overview
The `PowerSensor` is a template class that provides functionality for SinricPro devices to report various electrical power-related metrics. This is crucial for smart plugs, energy monitors, or other devices that need to provide detailed power consumption data. It allows devices to send events containing voltage, current, power, apparent power, reactive power, power factor, and calculated watt-hours.

## Template Parameters
*   `T`: The derived class that incorporates `PowerSensor`. This allows the controller to interact with the derived device's properties and methods (e.g., `deviceId`, `prepareEvent`, `sendEvent`, `getTimestamp`).

## Public Methods

### `PowerSensor()`
*   **Purpose**: Constructor for the `PowerSensor`. It initializes an `EventLimiter` to manage the rate of events sent.
*   **Parameters**: None
*   **Return Type**: None

### `sendPowerSensorEvent(float voltage, float current, float power = -1.0f, float apparentPower = -1.0f, float reactivePower = -1.0f, float factor = -1.0f, String cause = FSTR_SINRICPRO_PERIODIC_POLL)`
*   **Purpose**: Sends a `powerUsage` event to the SinricPro server, reporting various power-related measurements.
*   **Parameters**:
    *   `voltage`: A `float` representing the measured voltage.
    *   `current`: A `float` representing the measured current.
    *   `power`: (Optional) A `float` representing the active power (watts). If not provided (`-1.0f`), it will be calculated as `voltage * current`.
    *   `apparentPower`: (Optional) A `float` representing the apparent power (VA). If not provided (`-1.0f`), it is set to -1.
    *   `reactivePower`: (Optional) A `float` representing the reactive power (VAR). If not provided (`-1.0f`), it is set to -1.
    *   `factor`: (Optional) A `float` representing the power factor. If not provided (`-1.0f`) and `apparentPower` is provided, it is calculated as `power / apparentPower`.
    *   `cause`: (Optional) A `String` describing the reason why the event is sent (default is `"PERIODIC_POLL"`).
*   **Return Type**: `bool`
    *   `true`: The event has been sent successfully.
    *   `false`: The event has not been sent, possibly due to event rate limiting.
*   **Usage Example**:
    ```cpp
    // Assuming 'myEnergyMonitor' is an instance of a device class inheriting PowerSensor
    float currentVoltage = 230.5;
    float currentCurrent = 0.5;
    // myEnergyMonitor.sendPowerSensorEvent(currentVoltage, currentCurrent);

    // With all parameters
    // myEnergyMonitor.sendPowerSensorEvent(230.0, 1.2, 270.0, 276.0, 50.0, 0.98, "PHYSICAL_INTERACTION");
    ```

## Private Members (Internal)

### `startTime`
*   **Purpose**: Stores the timestamp when the power measurement period started, used for `wattHours` calculation.
*   **Type**: `unsigned long`

### `lastPower`
*   **Purpose**: Stores the last reported power value, used for `wattHours` calculation.
*   **Type**: `unsigned long`

### `getWattHours(unsigned long currentTimestamp)`
*   **Purpose**: Helper function to calculate watt-hours based on the time elapsed and the last reported power.
*   **Parameters**:
    *   `currentTimestamp`: The current timestamp.
*   **Return Type**: `float`

## Dependencies
*   `../EventLimiter.h`
*   `../SinricProStrings.h`
*   `../SinricProNamespace.h`

## Tags
`SinricPro`, `SDK`, `Capability`, `Power Sensor`, `Energy Monitoring`, `Sensor`, `Smart Home`, `ESP8266`, `ESP32`
