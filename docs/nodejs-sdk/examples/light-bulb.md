# Light Bulb Example

This example demonstrates how to control a smart light bulb using the SinricPro NodeJS SDK. It provides implementations for handling power state, color, color temperature, and brightness adjustments.

## Key Features

-   **Power State Control:** Implements `setPowerState` callback to handle turning the light bulb on or off.
-   **Color Temperature Control:** Implements `setColorTemperature` callback to adjust the color temperature of the light.
-   **Color Control:** Implements `setColor` callback to set the RGB color of the light.
-   **Brightness Control:** Implements `setBrightness` callback to adjust the brightness level of the light.
-   **Connection Management:** Includes `onDisconnect` and `onConnected` callbacks to handle the WebSocket connection status with SinricPro.

## Setup

1.  **Obtain Credentials:**
    *   Replace `APPKEY` with your SinricPro App Key.
    *   Replace `APPSECRET` with your SinricPro Secret Key.
    *   Replace `light` with the Device ID of your light bulb device.

    ```javascript
    const APPKEY = ''; // YOUR_APP_KEY
    const APPSECRET = ''; // YOUR_SECRET_KEY
    const light = ''; // YOUR_LIGHT_BULB_DEVICE_ID
    const deviceIds = [light];
    ```

2.  **Implement Callbacks:**
    Each function in the `callbacks` object will be invoked when SinricPro sends a corresponding command to your light bulb. Implement your device-specific logic within these functions to control your physical light bulb.

    ```javascript
    const setPowerState = async (deviceid, data) => {
      console.log("Power state: ", deviceid, data);
      return true;
    };

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
3.  Save the code as `light-bulb.js` (or any other `.js` file).
4.  Update the `APPKEY`, `APPSECRET`, and `light` with your actual SinricPro credentials and light bulb device ID.
5.  Execute the file:
    ```bash
    node light-bulb.js
    ```

The application will connect to SinricPro, and you will see console logs when commands are received for your light bulb. You can then control your light bulb via voice assistants or the SinricPro app.

## Code Snippet

```javascript
const {
  SinricPro, startSinricPro, raiseEvent, eventNames,
} = require('sinricpro');

const APPKEY = '';
const APPSECRET = '';
const light = '';
const deviceIds = [light];

const setPowerState = async (deviceid, data) => {
  console.log("Power state: ", deviceid, data);
  return true;
};

const setColorTemperature = async (deviceid, data) => {
  console.log("Color temperature: ", deviceid, data);
  return true;
};

const setColor = async (deviceid, data) => {
  console.log("Color: ", deviceid, data);
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
const callbacks = {
  setPowerState,
  setColorTemperature,
  setBrightness,
  setColor,
  onDisconnect,
  onConnected,
};

const sinricpro = new SinricPro(APPKEY, deviceIds, APPSECRET, true);
startSinricPro(sinricpro, callbacks);
```
