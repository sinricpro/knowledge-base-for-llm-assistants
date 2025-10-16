# AirQualitySensor

## Overview
The `AirQualitySensor` is a template class that provides functionality for SinricPro devices to report air quality measurements. Specifically, it allows devices to send data for PM1, PM2.5, and PM10 particle concentrations, which are crucial for environmental monitoring and smart home air quality systems.

## Template Parameters
*   `T`: The derived class that incorporates `AirQualitySensor`. This allows the controller to interact with the derived device's properties and methods (e.g., `deviceId`, `prepareEvent`, `sendEvent`).

## Public Methods

### `AirQualitySensor()`
*   **Purpose**: Constructor for the `AirQualitySensor`. It initializes an `EventLimiter` to manage the rate of events sent.
*   **Parameters**: None
*   **Return Type**: None

### `sendAirQualityEvent(int pm1 = 0, int pm2_5 = 0, int pm10 = 0, String cause = FSTR_SINRICPRO_PERIODIC_POLL)`
*   **Purpose**: Sends an `airQuality` event to the SinricPro server, reporting the measured particle concentrations.
*   **Parameters**:
    *   `pm1`: An `int` representing the 1.0 μm particle pollutant concentration in μg/m3 (default: 0).
    *   `pm2_5`: An `int` representing the 2.5 μm particle pollutant concentration in μg/m3 (default: 0).
    *   `pm10`: An `int` representing the 10 μm particle pollutant concentration in μg/m3 (default: 0).
    *   `cause`: (Optional) A `String` describing the reason why the event is sent (default is `"PERIODIC_POLL"`).
*   **Return Type**: `bool`
    *   `true`: The event has been sent successfully.
    *   `false`: The event has not been sent, possibly due to event rate limiting.
*   **Usage Example**:
    ```cpp
    // Assuming 'myAirSensor' is an instance of a device class inheriting AirQualitySensor
    int currentPM1 = 10;
    int currentPM2_5 = 25;
    int currentPM10 = 40;
    // myAirSensor.sendAirQualityEvent(currentPM1, currentPM2_5, currentPM10);
    ```

## Dependencies
*   `../EventLimiter.h`
*   `../SinricProStrings.h`
*   `../SinricProNamespace.h`

## Tags
`SinricPro`, `SDK`, `Capability`, `Air Quality`, `PM1`, `PM2.5`, `PM10`, `Sensor`, `Smart Home`, `Environmental`, `ESP8266`, `ESP32`
