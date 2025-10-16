# Brightness Control Example

This example demonstrates how to control the brightness and power state of a device using the SinricPro NodeJS SDK. It provides a basic implementation for handling `setPowerState` and `setBrightness` events, and also shows how to send brightness events back to SinricPro.

## Key Features

-   **Power State Control:** Implements a `setPowerState` callback to handle power on/off commands.
-   **Brightness Control:** Implements a `setBrightness` callback to handle brightness adjustment commands.
-   **Event Raising:** Demonstrates how to use `raiseEvent` to send the current brightness state of the device to the SinricPro platform.
-   **Connection Management:** Includes `onDisconnect` and `onConnected` callbacks to handle the WebSocket connection status with SinricPro.

## Setup

1.  **Obtain Credentials:**
    *   Replace `APPKEY` with your SinricPro App Key.
    *   Replace `APPSECRET` with your SinricPro Secret Key.
    *   Replace `light` with the Device ID of your light device.

    ```javascript
    const APPKEY = ''; // YOUR_APP_KEY
    const APPSECRET = ''; // YOUR_SECRET_KEY
    const light = ''; // YOUR_LIGHT_DEVICE_ID
    const deviceIds = [light];
    ```

2.  **Implement Callbacks:**
    The `setPowerState` and `setBrightness` functions in the `callbacks` object will be invoked when SinricPro sends corresponding commands. Implement your device-specific logic within these functions.

    ```javascript
    const setPowerState = async (deviceid, data) => {
      console.log(deviceid, data);
      return true;
    };

    const setBrightness = async (deviceid, data) => {
      console.log("Brightness: ", deviceid, data);
      return true;
    };
    ```

3.  **Send Brightness Events (Optional):**
    The `sendBrightness` function demonstrates how to proactively send brightness updates to SinricPro. This is useful if your device's brightness can be changed locally (e.g., via a physical button) and you want to synchronize its state with SinricPro.

    ```javascript
    function sendBrightness(sinricpro, deviceId, data) {
      try {
        raiseEvent(sinricpro, eventNames.setBrightness, deviceId, { brightness: data });
        return true;
      } catch (e) {
        return false;
      }
    }
    ```

4.  **Initialize and Start SinricPro:**
    The `SinricPro` instance is initialized with your credentials and device IDs. The `startSinricPro` function then establishes the connection and registers your callback functions.

    ```javascript
    const sinricpro = new SinricPro(APPKEY, deviceIds, APPSECRET, true);
    startSinricPro(sinricpro, callbacks);
    ```

## Usage

To run this example:

1.  Ensure you have Node.js installed.
2.  Install the SinricPro NodeJS SDK:
    ```bash
    npm install sinricpro
    ```
3.  Save the code as `brightness-control.js` (or any other `.js` file).
4.  Update the `APPKEY`, `APPSECRET`, and `light` with your actual SinricPro credentials and device ID.
5.  Execute the file:
    ```bash
    node brightness-control.js
    ```

The application will connect to SinricPro, and you will see console logs when brightness or power state commands are received. You can also uncomment the `setInterval` block to periodically send brightness updates.

## Code Snippet

```javascript
// process.env.SR_DEBUG = 1;

const {
  SinricPro, startSinricPro, raiseEvent, eventNames,
} = require('sinricpro');

const APPKEY = '';
const APPSECRET = '';
const light = '';
const deviceIds = [light];

const setPowerState = async (deviceid, data) => {
  console.log(deviceid, data);
  return true;
};

const setBrightness = async (deviceid, data) => {
  console.log("Brightness: ", deviceid, data);
  return true;
};

const onDisconnect = () => {
  console.log("Connection closed");
}

const onConnected = () => {
  console.log("Connected to Sinric Pro");
}

function sendBrightness(sinricpro, deviceId, data) {
  try {
    raiseEvent(sinricpro, eventNames.setBrightness, deviceId, { brightness: data });
    return true;
  } catch (e) {
    return false;
  }
}

const callbacks = {
  setPowerState,
  setBrightness,
  onDisconnect,
  onConnected,
};

const sinricpro = new SinricPro(APPKEY, deviceIds, APPSECRET, true /* restore device state */);
startSinricPro(sinricpro, callbacks);

// To change  brighness
// const brightnessValue = 30;

// setInterval(() => {
//   sendBrightness(sinricpro, light, brightnessValue)
// }, 60000);
```
