# SinricProClass

## Overview
The `SinricProClass` is the main class of the SinricPro library, responsible for handling communication between your ESP8266/ESP32 device and the SinricPro server. It manages device connections, message handling, and provides an interface for adding and managing smart home devices.

## Inheritance
`SinricProClass` inherits from `SinricProInterface`.

## Nested Classes

### SinricProClass::Proxy

#### Overview
The `Proxy` nested class is used internally by `SinricProClass::operator[]` to provide a convenient way to create or retrieve device instances. It allows for a more fluid syntax when working with devices.

#### Public Methods

##### `Proxy(SinricProClass* ptr, const String& deviceId)`
*   **Purpose**: Constructor for the `Proxy` class.
*   **Parameters**:
    *   `ptr`: A pointer to the `SinricProClass` instance.
    *   `deviceId`: The ID of the device.
*   **Return Type**: None

##### `operator DeviceType&()`
*   **Purpose**: Overloaded cast operator to allow the `Proxy` object to be implicitly converted to a reference of a `DeviceType`. This is how `SinricPro[DEVICE_ID]` works.
*   **Template Parameters**:
    *   `DeviceType`: The type of the device (e.g., `SinricProSwitch`, `SinricProLight`).
*   **Return Type**: A reference to the `DeviceType` instance.
*   **Usage Example**:
    ```cpp
    SinricProSwitch& mySwitch = SinricPro[SWITCH_ID];
    ```

## Callback Handlers (Typedefs)

### `ConnectedCallbackHandler`
*   **Purpose**: Callback definition for the `onConnected` function. Gets called when the device is connected to the SinricPro server.
*   **Signature**: `std::function<void(void)>`
*   **Parameters**: None
*   **Return Type**: `void`

### `DisconnectedCallbackHandler`
*   **Purpose**: Callback definition for the `onDisconnected` function. Gets called when the device is disconnected from the SinricPro server.
*   **Signature**: `std::function<void(void)>`
*   **Parameters**: None
*   **Return Type**: `void`

### `OTAUpdateCallbackHandler`
*   **Purpose**: Function signature for OTA (Over-The-Air) update callbacks. The callback function should accept a URL string and version details.
*   **Signature**: `std::function<bool(const String& url, int major, int minor, int patch, bool enforce)>`
*   **Parameters**:
    *   `url`: The URL from which the OTA update can be downloaded.
    *   `major`: The major version number.
    *   `minor`: The minor version number.
    *   `patch`: The patch version number.
    *   `enforce`: If `true`, skip the version check and apply the update.
*   **Return Type**: `bool` (True if the update process started successfully, false otherwise.)

### `SetSettingCallbackHandler`
*   **Purpose**: Function signature for setting a module setting. This callback is used to set a value for a specific setting identified by its ID.
*   **Signature**: `std::function<bool(const String& id, const String& value)>`
*   **Parameters**:
    *   `id`: The unique identifier of the setting to be set.
    *   `value`: The new value to be assigned to the setting.
*   **Return Type**: `bool` (Returns true if the setting was successfully updated, false otherwise.)

### `ReportHealthCallbackHandler`
*   **Purpose**: Defines a function type for reporting device health status.
*   **Signature**: `std::function<bool(String& healthReport)>`
*   **Parameters**:
    *   `healthReport`: A reference to a `String` that will contain the health status information. The callback function should populate this string with relevant health data.
*   **Return Type**: `bool` (Returns true if the health report was successfully handled, false otherwise.)

### `PongCallback`
*   **Purpose**: Callback for handling Pong messages from the WebSocket server.
*   **Signature**: `std::function<void(uint32_t)>`
*   **Parameters**:
    *   `uint32_t`: The payload of the Pong message.
*   **Return Type**: `void`

## Public Methods

### `begin(String appKey, String appSecret, String serverURL = SINRICPRO_SERVER_URL)`
*   **Purpose**: Initializes the `SinricProClass` to connect to the SinricPro Server.
*   **Parameters**:
    *   `appKey`: `String` containing your APP_KEY from SinricPro.
    *   `appSecret`: `String` containing your APP_SECRET from SinricPro.
    *   `serverURL`: `String` containing the SinricPro Server URL (default is `SINRICPRO_SERVER_URL`).
*   **Return Type**: `void`
*   **Usage Example**:
    ```cpp
    #define APP_KEY           "YOUR-APP-KEY"
    #define APP_SECRET        "YOUR-APP-SECRET"

    void setup() {
      SinricPro.begin(APP_KEY, APP_SECRET);
    }
    ```

### `handle()`
*   **Purpose**: Handles communication between the device and the SinricPro Server. This function must be called as often as possible, typically in your `loop()` function. It is responsible for connecting/disconnecting, and handling requests, responses, and events.
*   **Parameters**: None
*   **Return Type**: `void`
*   **Usage Example**:
    ```cpp
    void loop() {
      SinricPro.handle();
    }
    ```

### `stop()`
*   **Purpose**: Stops the SinricPro connection and related services.
*   **Parameters**: None
*   **Return Type**: `void`

### `isConnected()`
*   **Purpose**: Checks if the device is currently connected to the SinricPro server.
*   **Parameters**: None
*   **Return Type**: `bool` (True if connected, false otherwise.)

### `onConnected(ConnectedCallbackHandler cb)`
*   **Purpose**: Sets the callback function to be called when the device successfully connects to the SinricPro server.
*   **Parameters**:
    *   `cb`: Function pointer to a `ConnectedCallbackHandler` function.
*   **Return Type**: `void`

### `onDisconnected(DisconnectedCallbackHandler cb)`
*   **Purpose**: Sets the callback function to be called when the device disconnects from the SinricPro server.
*   **Parameters**:
    *   `cb`: Function pointer to a `DisconnectedCallbackHandler` function.
*   **Return Type**: `void`

### `onPong(PongCallback cb)`
*   **Purpose**: Sets the callback function to be called when a Pong message is received from the WebSocket server.
*   **Parameters**:
    *   `cb`: Function pointer to a `PongCallback` function.
*   **Return Type**: `void`

### `restoreDeviceStates(bool flag)`
*   **Purpose**: Enables or disables the feature to restore device states from the SinricPro server upon connection. If enabled, the server will send the last known states, triggering corresponding callbacks (e.g., `onPowerState`).
*   **Parameters**:
    *   `flag`: `true` to enable, `false` to disable.
*   **Return Type**: `void`

### `setResponseMessage(String&& message)`
*   **Purpose**: Sets a custom response message that will be included in the next response sent to the SinricPro server.
*   **Parameters**:
    *   `message`: An rvalue reference to a `String` containing the custom message.
*   **Return Type**: `void`

### `getTimestamp()`
*   **Purpose**: Returns the current timestamp (Unix epoch time) known by the SinricPro library.
*   **Parameters**: None
*   **Return Type**: `unsigned long`

### `sign(const String& message)`
*   **Purpose**: Signs a given message using the application secret. This is used for message integrity and authentication.
*   **Parameters**:
    *   `message`: The `String` message to be signed.
*   **Return Type**: `String` (The HMAC-SHA256 signature in base64 encoding.)

### `operator[](const String deviceId)`
*   **Purpose**: Overloaded `[]` operator to create a new device instance or retrieve an existing one. If the device ID is unknown, a new device instance is created.
*   **Parameters**:
    *   `deviceId`: A `String` containing the device ID.
*   **Return Type**: `SinricProClass::Proxy` (A proxy object representing a reference to a device derived from `SinricProDevice`.)
*   **Usage Example**:
    ```cpp
    #define SWITCH_ID         "YOUR-DEVICE-ID"
    // ...
    SinricProSwitch &mySwitch = SinricPro[SWITCH_ID];
    ```

### `onOTAUpdate(OTAUpdateCallbackHandler cb)`
*   **Purpose**: Sets the callback function for handling OTA (Over-The-Air) updates. This callback is invoked when an OTA update is available.
*   **Parameters**:
    *   `cb`: A function pointer or lambda of type `OTAUpdateCallbackHandler`.
*   **Return Type**: `void`

### `onSetSetting(SetSettingCallbackHandler cb)`
*   **Purpose**: Sets the callback function for handling module settings. This callback is invoked when a request to change a module setting is received.
*   **Parameters**:
    *   `cb`: A function pointer or lambda of type `SetSettingCallbackHandler`.
*   **Return Type**: `void`

### `onReportHealth(ReportHealthCallbackHandler cb)`
*   **Purpose**: Sets the callback function for reporting device health status. This callback is invoked when the SinricPro system requests the device's health status.
*   **Parameters**:
    *   `cb`: A function pointer or lambda of type `ReportHealthCallbackHandler`.
*   **Return Type**: `void`

## Protected Methods

### `add<typename DeviceType>(String deviceId)`
*   **Purpose**: Adds a new device instance to the SinricPro system. This is a template method allowing the addition of various device types.
*   **Template Parameters**:
    *   `DeviceType`: The type of the device to add (e.g., `SinricProSwitch`).
*   **Parameters**:
    *   `deviceId`: The ID of the device to add.
*   **Return Type**: `DeviceType&` (A reference to the newly added device instance.)
*   **Usage Example**:
    ```cpp
    SinricProSwitch& mySwitch = SinricPro.add<SinricProSwitch>("YOUR-SWITCH-ID");
    ```

### `add(SinricProDeviceInterface& newDevice)`
*   **Purpose**: (Deprecated) Adds a device by reference. Please use the template `add<DeviceType>(String)` method instead.
*   **Parameters**:
    *   `newDevice`: A reference to a `SinricProDeviceInterface` object.
*   **Return Type**: `void`

### `add(SinricProDeviceInterface* newDevice)`
*   **Purpose**: (Deprecated) Adds a device by pointer. Please use the template `add<DeviceType>(String)` method instead.
*   **Parameters**:
    *   `newDevice`: A pointer to a `SinricProDeviceInterface` object.
*   **Return Type**: `void`

### `prepareResponse(JsonDocument& requestMessage)`
*   **Purpose**: Prepares a JSON response message based on an incoming request message.
*   **Parameters**:
    *   `requestMessage`: A `JsonDocument` containing the incoming request.
*   **Return Type**: `JsonDocument` (The prepared JSON response.)

### `prepareEvent(String deviceId, const char* action, const char* cause)`
*   **Purpose**: Prepares a JSON event message to be sent to the SinricPro server.
*   **Parameters**:
    *   `deviceId`: The ID of the device sending the event.
    *   `action`: The action associated with the event (e.g., "setPowerState").
    *   `cause`: The cause of the event (e.g., "PHYSICAL_INTERACTION").
*   **Return Type**: `JsonDocument` (The prepared JSON event message.)

### `sendMessage(JsonDocument& jsonMessage)`
*   **Purpose**: Sends a JSON message to the SinricPro server.
*   **Parameters**:
    *   `jsonMessage`: A `JsonDocument` containing the message to send.
*   **Return Type**: `void`

## Dependencies
*   `SinricProDeviceInterface.h`
*   `SinricProInterface.h`
*   `SinricProMessageid.h`
*   `SinricProModuleCommandHandler.h`
*   `SinricProNamespace.h`
*   `SinricProQueue.h`
*   `SinricProSignature.h`
*   `SinricProStrings.h`
*   `SinricProUDP.h`
*   `SinricProWebsocket.h`
*   `Timestamp.h`

## Tags
`SinricPro`, `SDK`, `Main Class`, `Communication`, `ESP8266`, `ESP32`, `Smart Home`, `API`, `Callbacks`, `Device Management`
