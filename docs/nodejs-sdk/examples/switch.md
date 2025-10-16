# Basic Switch Example

This example demonstrates how to control a basic on/off switch device using the SinricPro NodeJS SDK. It focuses on handling the `setPowerState` event to toggle the device's power.

## Key Features

-   **Power State Control:** Implements a `setPowerState` callback to handle commands for turning the switch on or off.
-   **Connection Management:** Includes `onDisconnect` and `onConnected` callbacks to handle the WebSocket connection status with SinricPro.

## Setup

1.  **Obtain Credentials:**
    *   Replace `APPKEY` with your SinricPro App Key.
    *   Replace `APPSECRET` with your SinricPro Secret Key.
    *   Replace `device1` with the Device ID of your switch device.

    ```javascript
    const APPKEY = ''; // YOUR_APP_KEY
    const APPSECRET = ''; // YOUR_SECRET_KEY
    const device1 = ''; // YOUR_SWITCH_DEVICE_ID
    const deviceIds = [device1];
    ```

2.  **Implement Callbacks:**
    The `setPowerState` function in the `callbacks` object will be invoked when SinricPro sends a power state command to your switch. Implement your device-specific logic within this function.

    ```javascript
    const setPowerState = async (deviceid, data) => {
      console.log(deviceid, data);
      return true;
    };
    ```

3.  **Initialize and Start SinricPro:**
    The `SinricPro` instance is initialized with your credentials and device IDs. The `startSinricPro` function then establishes the connection and registers your callback functions.

    ```javascript
    const sinricpro = new SinricPro(APPKEY, deviceIds, APPSECRET, true);
    const callbacks = { setPowerState, onDisconnect, onConnected };
    startSinricPro(sinricpro, callbacks);
    ```

## Usage

To run this example:

1.  Ensure you have Node.js installed.
2.  Install the SinricPro NodeJS SDK:
    ```bash
    npm install sinricpro
    ```
3.  Save the code as `switch.js` (or any other `.js` file).
4.  Update the `APPKEY`, `APPSECRET`, and `device1` with your actual SinricPro credentials and switch device ID.
5.  Execute the file:
    ```bash
    node switch.js
    ```

The application will connect to SinricPro, and you will see console logs when power state commands are received for your switch. You can then control your switch via voice assistants or the SinricPro app.

## Code Snippet

```javascript
// process.env.SR_DEBUG = '1'; // Enable debug logs
const {
  SinricPro, startSinricPro, raiseEvent, eventNames,
} = require('sinricpro'); // require('../../index');

const APPKEY = '';
const APPSECRET = '';
const device1 = '';
const deviceIds = [device1];

const setPowerState = async (deviceid, data) => {
  console.log(deviceid, data);
  return true;
};

const onDisconnect = () => {
  console.log("Connection closed");
}

const onConnected = () => {
  console.log("Connected to Sinric Pro");
}

const sinricpro = new SinricPro(APPKEY, deviceIds, APPSECRET, true);
const callbacks = { setPowerState, onDisconnect, onConnected };

startSinricPro(sinricpro, callbacks);

// setInterval(() => {
//   // Send manual change the state in Sinric Pro
//   //raiseEvent(sinricpro, eventNames.powerState, device1, { state: 'On' });
//   //raiseEvent(sinricpro, eventNames.pushNotification, device1, { alert: "Hello there" });
// }, 10000);
```
