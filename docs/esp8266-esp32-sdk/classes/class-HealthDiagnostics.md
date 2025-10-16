# HealthDiagnostics Class

## Overview
The `HealthDiagnostics` class is a utility designed to collect various health and diagnostic metrics from an ESP board (or other Arduino-compatible microcontrollers) and format them into a JSON string. This information is primarily intended to be sent to the SinricPro server via the `onReportHealth` callback, providing insights into the device's operational status, memory usage, network connectivity, and firmware details.

## Public Methods

### `reportHealth(String& healthReport)`
*   **Purpose**: Gathers comprehensive diagnostic information about the microcontroller and populates the provided `healthReport` string with this data in JSON format. This method is typically called by the `SinricPro.onReportHealth` callback.
*   **Parameters**:
    *   `healthReport`: A reference to a `String` object. This string will be cleared and then populated with the JSON-formatted health data.
*   **Return Type**: `bool`
    *   `true`: The health report was successfully generated and populated.
    *   `false`: An error occurred during the generation of the health report.
*   **Example JSON Output (partial)**:
    ```json
    {
      "uptime": "1h 2m 3s",
      "freeHeap": 123456,
      "minFreeHeap": 120000,
      "heapFragmentation": 10,
      "wifiRSSI": -50,
      "localIP": "192.168.1.100",
      "sketchSize": 102400,
      "resetReason": "Power On"
    }
    ```

## Private Methods (Internal Implementation Details)

### `getChipId()`
*   **Purpose**: Retrieves the unique identifier of the microcontroller chip.
*   **Return Type**: `String`

### `addHeapInfo(JsonObject& doc)`
*   **Purpose**: Adds heap memory-related information (e.g., free heap, minimum free heap, heap fragmentation) to the provided JSON object.
*   **Parameters**:
    *   `doc`: A reference to an `ArduinoJson::JsonObject` where heap information will be added.
*   **Return Type**: `void`

### `addWiFiInfo(JsonObject& doc)`
*   **Purpose**: Adds Wi-Fi related information (e.g., RSSI, local IP address, SSID) to the provided JSON object.
*   **Parameters**:
    *   `doc`: A reference to an `ArduinoJson::JsonObject` where Wi-Fi information will be added.
*   **Return Type**: `void`

### `addSketchInfo(JsonObject& doc)`
*   **Purpose**: Adds information about the currently running sketch (firmware), such as its size, to the provided JSON object.
*   **Parameters**:
    *   `doc`: A reference to an `ArduinoJson::JsonObject` where sketch information will be added.
*   **Return Type**: `void`

### `addResetCause(JsonObject& doc)`
*   **Purpose**: Adds information about the last reset cause of the microcontroller to the provided JSON object.
*   **Parameters**:
    *   `doc`: A reference to an `ArduinoJson::JsonObject` where reset cause information will be added.
*   **Return Type**: `void`

## Dependencies
*   `Arduino.h`
*   `ArduinoJson.h`
*   `WiFi.h` (or `ESP8266WiFi.h`)
*   `esp_system.h`, `esp_wifi.h`, `esp_heap_caps.h` (for ESP32)
*   `user_interface.h`, `umm_malloc/umm_heap_select.h`, `umm_malloc/umm_malloc.h` (for ESP8266)

## Tags
`SinricPro`, `Utility`, `Health`, `Diagnostics`, `ESP32`, `ESP8266`, `Arduino`, `Monitoring`, `System Info`
