# TemperatureSensor

## Overview
The `TemperatureSensor` is a template class that provides functionality for SinricPro devices to report temperature and, optionally, humidity readings from a connected sensor. This is a fundamental capability for smart home devices like thermostats, weather stations, or environmental monitors.

## Template Parameters
*   `T`: The derived class that incorporates `TemperatureSensor`. This allows the controller to interact with the derived device's properties and methods (e.g., `deviceId`, `prepareEvent`, `sendEvent`).

## Public Methods

### `TemperatureSensor()`
*   **Purpose**: Constructor for the `TemperatureSensor`. It initializes an `EventLimiter` to manage the rate of events sent.
*   **Parameters**: None
*   **Return Type**: None

### `sendTemperatureEvent(float temperature, float humidity = -1, String cause = FSTR_SINRICPRO_PERIODIC_POLL)`
*   **Purpose**: Sends a `currentTemperature` event to the SinricPro server, reporting the actual temperature and optionally humidity measured by a sensor.
*   **Parameters**:
    *   `temperature`: A `float` representing the actual temperature measured by the sensor.
    *   `humidity`: (Optional) A `float` representing the actual humidity measured by the sensor. A default value of `-1.0f` indicates that humidity is not supported or not available.
    *   `cause`: (Optional) A `String` describing the reason why the event is sent (default is `"PERIODIC_POLL"`).
*   **Return Type**: `bool`
    *   `true`: The event has been sent successfully.
    *   `false`: The event has not been sent, possibly due to event rate limiting.
*   **Usage Example**:
    ```cpp
    // Assuming 'myTempSensor' is an instance of a device class inheriting TemperatureSensor
    float currentTemp = 25.5;
    float currentHumidity = 60.2;
    // myTempSensor.sendTemperatureEvent(currentTemp, currentHumidity);

    // If humidity is not available
    // myTempSensor.sendTemperatureEvent(currentTemp);
    ```

## Dependencies
*   `../EventLimiter.h`
*   `../SinricProStrings.h`
*   `../SinricProNamespace.h`

## Tags
`SinricPro`, `SDK`, `Capability`, `Temperature Sensor`, `Humidity Sensor`, `Sensor`, `Smart Home`, `ESP8266`, `ESP32`
