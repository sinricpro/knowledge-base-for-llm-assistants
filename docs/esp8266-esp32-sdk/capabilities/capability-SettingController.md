# SettingController

## Overview
The `SettingController` is a template class that provides functionality for SinricPro devices to manage and respond to requests for setting various device-specific configurations or settings. This allows for dynamic adjustment of device parameters that are not covered by other specific capabilities.

## Template Parameters
*   `T`: The derived class that incorporates `SettingController`. This allows the controller to interact with the derived device's properties and methods (e.g., `deviceId`).

## Callback Handlers (Typedefs)

### `SetSettingCallback`
*   **Purpose**: Defines the signature for the callback function that is invoked when the device receives a `setSetting` request from the SinricPro server.
*   **Signature**: `std::function<bool(const String& deviceId, const String& settingId, String& settingValue)>`
*   **Parameters**:
    *   `deviceId`: A `String` containing the unique ID of the device.
    *   `settingId`: A `String` representing the unique identifier of the setting to be changed.
    *   `settingValue`: A `String` reference. On input, it represents the requested value for the setting. The callback should update this parameter to reflect the actual value set by the device.
*   **Return Type**: `bool`
    *   `true`: The request was handled successfully.
    *   `false`: The request could not be handled properly.
*   **Usage Example**:
    ```cpp
    bool onSetSetting(const String &deviceId, const String &settingId, String &settingValue) {
      Serial.printf("Device %s received setSetting for %s to %s\r\n", deviceId.c_str(), settingId.c_str(), settingValue.c_str());
      // Your code to apply the setting
      return true;
    }
    // In setup:
    // myDevice.onSetSetting(onSetSetting);
    ```

## Public Methods

### `SettingController()`
*   **Purpose**: Constructor for the `SettingController`. It registers a request handler with the derived device class to process setting-related requests.
*   **Parameters**: None
*   **Return Type**: None

### `onSetSetting(SetSettingCallback cb)`
*   **Purpose**: Sets the callback function that will be invoked when the SinricPro server sends a `setSetting` command to the device.
*   **Parameters**:
    *   `cb`: A `SetSettingCallback` function pointer or lambda that will handle the setting change request.
*   **Return Type**: `void`

## Protected Methods

### `handleSettingController(SinricProRequest &request)`
*   **Purpose**: Internal method used by the `SettingController` to process incoming `SinricProRequest` objects. It checks if the request is a `setSetting` action and, if so, invokes the registered `SetSettingCallback`.
*   **Parameters**:
    *   `request`: A reference to the `SinricProRequest` object received from the server.
*   **Return Type**: `bool` (Indicates if the request was handled by this controller.)

## Dependencies
*   `../SinricProRequest.h`
*   `../SinricProStrings.h`
*   `../SinricProNamespace.h`

## Tags
`SinricPro`, `SDK`, `Capability`, `Settings`, `Configuration`, `Controller`, `Smart Home`, `ESP8266`, `ESP32`
