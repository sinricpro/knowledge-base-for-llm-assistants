# Thermostat Example

This example demonstrates how to control a thermostat device using the SinricPro NodeJS SDK. It provides implementations for handling power state, thermostat mode, and target temperature adjustments.

## Key Features

-   **Power State Control:** Implements `setPowerState` callback to handle turning the thermostat on or off.
-   **Thermostat Mode Control:** Implements `setThermostatMode` callback to set the operating mode of the thermostat (e.g., AUTO, HEAT, COOL).
-   **Target Temperature Setting:** Implements `targetTemperature` callback to set a specific target temperature.
-   **Adjust Target Temperature:** Implements `adjustTargetTemperature` callback to incrementally adjust the target temperature.
-   **Connection Management:** Includes `onDisconnect` and `onConnected` callbacks to handle the WebSocket connection status with SinricPro.

## Setup

1.  **Obtain Credentials:**
    *   Replace `appKey` with your SinricPro App Key.
    *   Replace `secretKey` with your SinricPro Secret Key.
    *   Replace `deviceIds` with the Device ID of your thermostat device.

    ```javascript
    const appKey = ""; // YOUR_APP_KEY
    const secretKey = ""; // YOUR_SECRET_KEY
    const deviceIds = [""]; // YOUR_THERMOSTAT_DEVICE_ID
    ```

2.  **Implement Callbacks:**
    Each function in the `callbacks` object will be invoked when SinricPro sends a corresponding command to your thermostat. Implement your device-specific logic within these functions.

    ```javascript
    function setPowerState(deviceid, data) {
      console.log("Power state: ", deviceid, data);
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
4.  Update the `appKey`, `secretKey`, and `deviceIds` with your actual SinricPro credentials and thermostat device ID.
5.  Execute the file:
    ```bash
    node app.js
    ```

The application will connect to SinricPro, and you will see console logs when commands are received for your thermostat. You can then control your thermostat via voice assistants or the SinricPro app.

## Code Snippet

```javascript
//process.env.SR_DEBUG = '1'; // Enable debug logs

const { SinricPro, startSinricPro, eventNames, raiseEvent } = require('sinricpro');

const appKey = "";
const secretKey = "";
const deviceIds = [""];

let globalTemperature = 0;

function setPowerState(deviceid, data) {
  console.log("Power state: ", deviceid, data);
  return true;
}
 
function setThermostatMode(deviceid, data) {
  console.log("Thermostat mode: ", deviceid, data);
  return true;
}

function targetTemperature(deviceid, temperature) {
    console.log("Target temperature: ", deviceid, temperature); 
    globalTemperature = temperature;
    return true;
}

function adjustTargetTemperature(deviceid, temperatureDelta) {
  console.log("Adjust target temperature by ", temperatureDelta); 
  console.log("Current temperature is: ", globalTemperature);
  globalTemperature += temperatureDelta;  // calculate absolut temperature
  console.log("New temperature is: ", globalTemperature);
  return { success: true, temperature: globalTemperature };
}

const onDisconnect = () => {
  console.log("Connection closed");
}

const onConnected = () => {
  console.log("Connected to Sinric Pro");
}

const callbacks = {
  setPowerState,
  targetTemperature,
  setThermostatMode,
  adjustTargetTemperature,
  onDisconnect,
  onConnected,
};

const sinricpro = new SinricPro(appKey, deviceIds, secretKey, true);
startSinricPro(sinricpro, callbacks);

//  setInterval(() => {
//     //raiseEvent(sinricpro, eventNames.powerState, "deviceid", { state: 'On' });
//     //raiseEvent(sinricpro, eventNames.targetTemperature, "deviceid", { temperature: Math.floor(Math.random() * 60) });
//     //raiseEvent(sinricpro, eventNames.thermostatMode, "deviceid", { thermostatMode: 'AUTO' });
//     //raiseEvent(sinricpro, eventNames.currentTemperature, "deviceid", { "humidity": 75.3, "temperature": 24});
//  }, 15000);
```
