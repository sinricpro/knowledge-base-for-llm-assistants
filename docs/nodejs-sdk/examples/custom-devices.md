# Custom Devices Example

This example demonstrates how to integrate and control custom device types using the SinricPro NodeJS SDK. It focuses on implementing callbacks for common custom device functionalities such as setting modes, managing range values, and toggling states.

## Key Features

-   **Custom Mode Control:** Implements `setMode` callback to handle commands for changing the operational mode of a custom device.
-   **Range Value Management:** Includes `setRangeValue` and `adjustRangeValue` callbacks for controlling devices that operate within a specific numerical range (e.g., fan speed, dimmer levels).
-   **Toggle State Control:** Provides a `setToggleState` callback for devices that have a simple on/off or enabled/disabled state.
-   **Connection Management:** Includes `onDisconnect` and `onConnected` callbacks to handle the WebSocket connection status with SinricPro.

## Setup

1.  **Obtain Credentials:**
    *   Replace `APPKEY` with your SinricPro App Key.
    *   Replace `APPSECRET` with your SinricPro Secret Key.
    *   Replace `device1` with the Device ID of your custom device.

    ```javascript
    const APPKEY = ""; // YOUR_APP_KEY
    const APPSECRET = ""; // YOUR_SECRET_KEY
    const device1 = ""; // YOUR_CUSTOM_DEVICE_ID
    const deviceIds = [device1];
    ```

2.  **Implement Callbacks:**
    Each function in the `callbacks` object (`setMode`, `setRangeValue`, `adjustRangeValue`, `setToggleState`) will be invoked when SinricPro sends a corresponding command to your custom device. Implement your device-specific logic within these functions.

    ```javascript
    async function setMode(deviceid, data, instanceId) {
      console.log(deviceid, data, instanceId);
      return true;
    }

    // ... other callback functions
    ```

3.  **Initialize and Start SinricPro:**
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
3.  Save the code as `custom-devices.js` (or any other `.js` file).
4.  Update the `APPKEY`, `APPSECRET`, and `device1` with your actual SinricPro credentials and custom device ID.
5.  Execute the file:
    ```bash
    node custom-devices.js
    ```

The application will connect to SinricPro, and you will see console logs when commands are received for your custom device.

## Code Snippet

```javascript
//process.env.SR_DEBUG = '1';

const { SinricPro, startSinricPro } = require("sinricpro"); //= require('../../index');

const APPKEY = "";
const APPSECRET = "";
const device1 = "";

const deviceIds = [device1];

async function setMode(deviceid, data, instanceId) {
  console.log(deviceid, data, instanceId);
  return true;
}

async function setRangeValue(deviceid, data, instanceId) {
  console.log(deviceid, data, instanceId);
  return true;
}

async function adjustRangeValue(deviceid, data, instanceId) {
  console.log(deviceid, data, instanceId);
  return true;
}

async function setToggleState(deviceid, data, instanceId) {
  console.log(deviceid, data, instanceId);
  return true;
}

const onDisconnect = () => {
  console.log("Connection closed");
}

const onConnected = () => {
  console.log("Connected to Sinric Pro");
}

const callbacks = {
  adjustRangeValue,
  setMode,
  setRangeValue,
  setToggleState,
  onDisconnect,
  onConnected,
};

const sinricpro = new SinricPro(APPKEY, deviceIds, APPSECRET, true);
startSinricPro(sinricpro, callbacks);
```
