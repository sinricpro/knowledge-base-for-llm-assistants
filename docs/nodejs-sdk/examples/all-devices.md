# All Devices Example

This example demonstrates how to handle various device types and their corresponding SinricPro events within a single application. It showcases the implementation of callbacks for a wide range of device functionalities, including power control, brightness adjustment, color settings, thermostat modes, range values, volume control, media playback, and more.

## Key Features

-   **Comprehensive Device Control:** Implements callbacks for numerous device capabilities such as `setPowerState`, `setBrightness`, `setColor`, `setThermostatMode`, `setRangeValue`, `setVolume`, `selectInput`, `mediaControl`, `changeChannel`, `setBands`, `setMode`, `setMute`, and `setLockState`.
-   **Event Handling:** Demonstrates how to define and register callback functions that are invoked when SinricPro events are received for specific device actions.
-   **Connection Management:** Includes `onDisconnect` and `onConnected` callbacks to handle the WebSocket connection status with SinricPro.

## Setup

1.  **Obtain Credentials:**
    *   Replace `appKey` with your SinricPro App Key.
    *   Replace `secretKey` with your SinricPro Secret Key.
    *   Replace `deviceIds` with an array of your device IDs.

    ```javascript
    const appKey = ""; // YOUR_APP_KEY
    const secretKey = ""; // YOUR_SECRET_KEY
    const deviceIds = [""]; // YOUR_DEVICE_ID(s)
    ```

2.  **Implement Callbacks:**
    Each function in the `callbacks` object corresponds to a specific SinricPro event. You need to implement the logic for each of these functions based on your device's capabilities. The example provides basic `console.log` statements for demonstration.

    ```javascript
    function setPowerState(deviceid, data) {
      console.log(deviceid, data);
      return true;
    }

    // ... other callback functions
    ```

3.  **Initialize and Start SinricPro:**
    The `SinricPro` instance is initialized with your credentials and device IDs. The `startSinricPro` function then establishes the connection and registers your callback functions.

    ```javascript
    const sinricpro = new SinricPro(appKey, deviceIds, secretKey, true);
    startSinricPro(sinricpro, callbacks);
    ```

## Usage

To run this example:

1.  Ensure you have Node.js installed.
2.  Install the SinricPro NodeJS SDK:
    ```bash
    npm install sinricpro
    ```
3.  Save the code as `app.js` (or any other `.js` file).
4.  Update the `appKey`, `secretKey`, and `deviceIds` with your actual SinricPro credentials.
5.  Execute the file:
    ```bash
    node app.js
    ```

The application will connect to SinricPro, and you will see console logs when events are triggered from the SinricPro platform (e.g., via the SinricPro mobile app or voice assistants).

## Code Snippet

```javascript
const { SinricPro, startSinricPro, eventNames } = require('sinricpro');

const appKey = ""; // YOUR_APP_KEY
const secretKey = ""; // YOUR_SECRET_KEY
const deviceIds = [""]; // YOUR_DEVICE_ID(s)

function setPowerState(deviceid, data) {
  console.log(deviceid, data);
  return true;
}

function setPowerLevel(deviceid, data) {
  console.log(deviceid, data);
  return true;
}

function adjustPowerLevel(deviceid, data) {
  console.log(deviceid, data);
  return true;
}

function setBrightness(deviceid, data) {
  console.log(deviceid, data);
  return true;
}

function adjustBrightness(deviceid, data) {
  console.log(deviceid, data);
  return true;
}

function setColor(deviceid, data) {
  console.log(deviceid, data);
  return true;
}

function setThermostatMode(deviceid, data) {
  console.log(deviceid, data);
  return true;
}

function setRangeValue(deviceid, data) {
  console.log(deviceid, data);
  return true;
}

function adjustRangeValue(deviceid, data) {
  console.log(deviceid, data);
  return true;
}

function setVolume(deviceid, data) {
  console.log(deviceid, data);
  return true;
}

function adjustVolume(deviceid, data) {
  console.log(deviceid, data);
  return true;
}

function selectInput(deviceid, data) {
  console.log(deviceid, data);
  return true;
}

function mediaControl(deviceid, data) {
  console.log(deviceid, data);
  return true;
}

function changeChannel(deviceid, data) {
  console.log(deviceid, data);
  return true;
}

function skipChannels(deviceid, data) {
  console.log(deviceid, data);
  return true;
}

function setBands(deviceid, data) {
  console.log(deviceid, data);
  return true;
}

function adjustBands(deviceid, data) {
  console.log(deviceid, data);
  return true;
}

function setMode(deviceid, data) {
  console.log(deviceid, data);
  return true;
}

function setMute(deviceid, data) {
  console.log(deviceid, data);
  return true;
}

function setLockState(deviceid, data) {
  console.log(deviceid, data);
  return true;
}

const onDisconnect = () => {
  console.log("Connection closed");
}

const onConnected = () => {
  console.log("Connected to Sinric Pro");
}

const callbacks = {
  setPowerState,
  setPowerLevel,
  adjustPowerLevel,
  setColor,
  setRangeValue,
  setLockState,
  setBrightness,
  setVolume,
  adjustVolume,
  setMute,
  adjustBrightness,
  setThermostatMode,
  adjustRangeValue,
  selectInput,
  mediaControl,
  skipChannels,
  changeChannel,
  setBands,
  setMode,
  adjustBands,
  onDisconnect,
  onConnected,
};

const sinricpro = new SinricPro(appKey, deviceIds, secretKey, true);
startSinricPro(sinricpro, callbacks);
```
