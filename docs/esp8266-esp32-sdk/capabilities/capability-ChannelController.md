# ChannelController

## Overview
The `ChannelController` is a template class that provides functionality for SinricPro devices to control and report channel changes. This is primarily used for devices like smart TVs or set-top boxes, allowing users to change channels by name or number, and to skip channels. It also enables the device to report its current channel to the SinricPro server.

## Template Parameters
*   `T`: The device type that this controller is attached to. This allows the controller to interact with the derived device's properties and methods (e.g., `deviceId`, `prepareEvent`, `sendEvent`).

## Callback Handlers (Typedefs)

### `ChangeChannelCallback`
*   **Purpose**: Defines the signature for the callback function that is invoked when the device receives a `changeChannel` request by channel name.
*   **Signature**: `std::function<bool(const String &deviceId, String &channel)>`
*   **Parameters**:
    *   `deviceId`: A `String` containing the unique ID of the device.
    *   `channel`: A `String` reference. On input, it represents the requested channel name. The callback should update this parameter to reflect the actual channel name switched to by the device.
*   **Return Type**: `bool`
    *   `true`: The request was handled successfully.
    *   `false`: The request could not be handled properly.
*   **Usage Example**:
    ```cpp
    bool onChangeChannel(const String &deviceId, String &channelName) {
      Serial.printf("Device %s requested to change to channel: %s\r\n", deviceId.c_str(), channelName.c_str());
      // Your code to change the channel by name
      return true;
    }
    // In setup:
    // myTV.onChangeChannel(onChangeChannel);
    ```

### `ChangeChannelNumberCallback`
*   **Purpose**: Defines the signature for the callback function that is invoked when the device receives a `changeChannel` request by channel number.
*   **Signature**: `std::function<bool(const String &deviceId, int channelNumber, String &channelName)>`
*   **Parameters**:
    *   `deviceId`: A `String` containing the unique ID of the device.
    *   `channelNumber`: An `int` representing the requested channel number.
    *   `channelName`: A `String` reference. The callback should update this parameter to reflect the actual channel name switched to by the device.
*   **Return Type**: `bool`
    *   `true`: The request was handled successfully.
    *   `false`: The request could not be handled properly.
*   **Usage Example**:
    ```cpp
    bool onChangeChannelNumber(const String &deviceId, int channelNumber, String &channelName) {
      Serial.printf("Device %s requested to change to channel number: %d\r\n", deviceId.c_str(), channelNumber);
      // Your code to change the channel by number
      channelName = "Channel " + String(channelNumber); // Example: update channelName
      return true;
    }
    // In setup:
    // myTV.onChangeChannelNumber(onChangeChannelNumber);
    ```

### `SkipChannelsCallback`
*   **Purpose**: Defines the signature for the callback function that is invoked when the device receives a `skipChannels` request.
*   **Signature**: `std::function<bool(const String &deviceId, int channelCount, String &channelName)>`
*   **Parameters**:
    *   `deviceId`: A `String` containing the unique ID of the device.
    *   `channelCount`: An `int` representing the number of channels to skip (`-n` for previous, `+n` for next).
    *   `channelName`: A `String` reference. The callback should update this parameter to reflect the actual channel name switched to by the device.
*   **Return Type**: `bool`
    *   `true`: The request was handled successfully.
    *   `false`: The request could not be handled properly.
*   **Usage Example**:
    ```cpp
    bool onSkipChannels(const String &deviceId, int channelCount, String &channelName) {
      Serial.printf("Device %s requested to skip %d channels\r\n", deviceId.c_str(), channelCount);
      // Your code to skip channels
      channelName = "New Channel"; // Example: update channelName
      return true;
    }
    // In setup:
    // myTV.onSkipChannels(onSkipChannels);
    ```

## Public Methods

### `ChannelController()`
*   **Purpose**: Constructor for the `ChannelController`. It initializes an `EventLimiter` and registers a request handler with the derived device class to process channel-related requests.
*   **Parameters**: None
*   **Return Type**: None

### `onChangeChannel(ChangeChannelCallback cb)`
*   **Purpose**: Sets the callback function that will be invoked when the SinricPro server sends a `changeChannel` command by channel name to the device.
*   **Parameters**:
    *   `cb`: A `ChangeChannelCallback` function pointer or lambda.
*   **Return Type**: `void`

### `onChangeChannelNumber(ChangeChannelNumberCallback cb)`
*   **Purpose**: Sets the callback function that will be invoked when the SinricPro server sends a `changeChannel` command by channel number to the device.
*   **Parameters**:
    *   `cb`: A `ChangeChannelNumberCallback` function pointer or lambda.
*   **Return Type**: `void`

### `onSkipChannels(SkipChannelsCallback cb)`
*   **Purpose**: Sets the callback function that will be invoked when the SinricPro server sends a `skipChannels` command to the device.
*   **Parameters**:
    *   `cb`: A `SkipChannelsCallback` function pointer or lambda.
*   **Return Type**: `void`

### `sendChangeChannelEvent(String channelName, String cause = FSTR_SINRICPRO_PHYSICAL_INTERACTION)`
*   **Purpose**: Sends a `changeChannel` event to the SinricPro server, informing it about the device's current channel. This is typically called when the device's channel changes locally.
*   **Parameters**:
    *   `channelName`: A `String` indicating the current channel name.
    *   `cause`: (Optional) A `String` describing the reason for the event (default is `"PHYSICAL_INTERACTION"`).
*   **Return Type**: `bool`
    *   `true`: The event was sent successfully.
    *   `false`: The event was not sent, possibly due to event rate limiting.
*   **Usage Example**:
    ```cpp
    // After changing channel locally
    // myTV.sendChangeChannelEvent("CNN");
    ```

## Protected Methods

### `handleChannelController(SinricProRequest &request)`
*   **Purpose**: Internal method used by the `ChannelController` to process incoming `SinricProRequest` objects. It checks if the request is a `changeChannel` (by name or number) or `skipChannels` action and, if so, invokes the corresponding registered callback.
*   **Parameters**:
    *   `request`: The incoming request to process.
*   **Return Type**: `bool` (Indicates if the request was handled by this controller.)

## Dependencies
*   `../SinricProRequest.h`
*   `../EventLimiter.h`
*   `../SinricProStrings.h`
*   `../SinricProNamespace.h`

## Tags
`SinricPro`, `SDK`, `Capability`, `Channel`, `TV`, `Controller`, `Smart Home`, `ESP8266`, `ESP32`
