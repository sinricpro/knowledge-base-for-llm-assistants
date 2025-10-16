# CameraController

## Overview
The `CameraController` is a template class that provides functionality for SinricPro camera devices. It enables cameras to respond to requests for snapshots and to send both snapshot images and motion detection data to the SinricPro server. This controller includes platform-specific (ESP32) implementations for handling HTTP client operations required for data transfer.

## Template Parameters
*   `T`: The device type that this controller is attached to. This allows the controller to interact with the derived device's properties and methods (e.g., `deviceId`, `getTimestamp`, `sign`).

## Callback Handlers (Typedefs)

### `SnapshotCallback`
*   **Purpose**: Defines the signature for the callback function that is invoked when the device receives a `getSnapshot` request from the SinricPro server.
*   **Signature**: `std::function<bool(const String &deviceId)>`
*   **Parameters**:
    *   `deviceId`: A `String` containing the unique ID of the device for which the snapshot is requested.
*   **Return Type**: `bool`
    *   `true`: The snapshot request was handled successfully (e.g., the device initiated a snapshot capture).
    *   `false`: The request could not be handled properly.
*   **Usage Example**:
    ```cpp
    bool onSnapshot(const String &deviceId) {
      Serial.printf("Device %s received snapshot request\r\n", deviceId.c_str());
      // Your code to capture a snapshot (e.g., from camera module)
      // Then call sendSnapshot with the captured image data
      return true;
    }
    // In setup:
    // myCamera.onSnapshot(onSnapshot);
    ```

## Public Methods

### `CameraController()`
*   **Purpose**: Constructor for the `CameraController`. It initializes an `EventLimiter` and registers a request handler with the derived device class to process camera control requests.
*   **Parameters**: None
*   **Return Type**: None

### `onSnapshot(SnapshotCallback cb)`
*   **Purpose**: Sets the callback function that will be invoked when the SinricPro server sends a `getSnapshot` command to the device.
*   **Parameters**:
    *   `cb`: A `SnapshotCallback` function pointer or lambda that will handle the snapshot request.
*   **Return Type**: `void`

### `sendSnapshot(uint8_t* buffer, size_t len)`
*   **Purpose**: Sends a camera snapshot (image data) to the SinricPro server. This function is typically called from within the `onSnapshot` callback after an image has been captured.
*   **Parameters**:
    *   `buffer`: A pointer to the `uint8_t` array containing the image data.
    *   `len`: The length of the image data in bytes.
*   **Return Type**: `int` (Returns an HTTP status code or -1 on error. Specific to ESP32 implementation.)
*   **Usage Example**:
    ```cpp
    // Inside onSnapshot callback after capturing image
    // int result = myCamera.sendSnapshot(imageDataBuffer, imageDataLength);
    // if (result == 200) Serial.println("Snapshot sent successfully");
    ```

### `sendMotion(fs::FS &fs, const char * path)`
*   **Purpose**: Sends motion detection data from a file (e.g., a recorded video clip or a series of images) to the SinricPro server. This is specific to ESP32 and uses the filesystem.
*   **Parameters**:
    *   `fs`: A reference to the filesystem object (e.g., `SPIFFS`, `LittleFS`).
    *   `path`: A C-style string representing the path to the motion data file.
*   **Return Type**: `int` (Returns an HTTP status code or -1 on error. Specific to ESP32 implementation.)

## Protected Methods

### `handleCameraController(SinricProRequest &request)`
*   **Purpose**: Internal method used by the `CameraController` to process incoming `SinricProRequest` objects. It checks if the request is a `getSnapshot` action and, if so, invokes the registered `SnapshotCallback`.
*   **Parameters**:
    *   `request`: The incoming request to process.
*   **Return Type**: `bool` (Indicates if the request was handled by this controller.)

## Private Members

### `getSnapshotCallback`
*   **Purpose**: Stores the user-defined callback function for `getSnapshot` requests.
*   **Type**: `SnapshotCallback`

### `event_limiter`
*   **Purpose**: An `EventLimiter` object used to control the rate at which events (like snapshots or motion data) are sent to prevent flooding the server.
*   **Type**: `EventLimiter`

## Dependencies
*   `../SinricProRequest.h`
*   `../EventLimiter.h`
*   `../SinricProStrings.h`
*   `../SinricProNamespace.h`
*   `<FS.h>`
*   `<HTTPClient.h>` (ESP32 only)
*   `<WiFiClientSecure.h>` (ESP32 only)

## Tags
`SinricPro`, `SDK`, `Capability`, `Camera`, `Snapshot`, `Motion Detection`, `Controller`, `Smart Home`, `ESP8266`, `ESP32`
