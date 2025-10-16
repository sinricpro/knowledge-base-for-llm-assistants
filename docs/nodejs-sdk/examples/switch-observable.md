# Observable Switch Example

This example demonstrates how to control a switch device using the SinricPro NodeJS SDK with an observable pattern. It showcases how to use `startSinricProObservable` to manage the connection and periodically raise events, such as the power state.

## Key Features

-   **Power State Control:** Implements a `setPowerState` callback to handle commands for turning the switch on or off.
-   **Observable Connection Management:** Utilizes `startSinricProObservable` to establish and manage the connection to SinricPro, providing an observable stream for connection events.
-   **Periodic Event Raising:** Demonstrates how to use `setInterval` within the observable's subscription to periodically send device state updates (e.g., `powerState`) to SinricPro.
-   **Connection Handling:** Clears the interval when the connection is closed.

## Setup

1.  **Obtain Credentials:**
    *   Replace `appKey` with your SinricPro App Key.
    *   Replace `secretKey` with your SinricPro Secret Key.
    *   Replace `device1` and `device2` with the Device IDs of your switch devices.

    ```javascript
    const appKey = ''; // YOUR_APP_KEY
    const secretKey = ''; // YOUR_SECRET_KEY
    const device1 = ''; // YOUR_SWITCH_DEVICE_ID_1
    const device2 = ''; // YOUR_SWITCH_DEVICE_ID_2
    const deviceId = [device1, device2];
    ```

2.  **Implement Callbacks:**
    The `setPowerState` function in the `callbacks` object will be invoked when SinricPro sends a power state command to your switch. Implement your device-specific logic within this function.

    ```javascript
    function setPowerState(deviceid, data) {
      console.log(deviceid, data);
      return true;
    }
    ```

3.  **Initialize and Start SinricPro with Observable:**
    The `SinricPro` instance is initialized with your credentials and device IDs. `startSinricProObservable` is then used to subscribe to connection events and manage periodic event raising.

    ```javascript
    const sinricpro = new SinricPro(appKey, deviceId, secretKey, true);

    let interval = null;

    startSinricProObservable(sinricpro, callbacks).subscribe((emit) => {
      console.log(emit);
      // const udp = new SinricProUdp(deviceId, secretKey); // Note: SinricProUdp is not imported in this example
      // udp.begin(callbacks);

      interval = setInterval(() => {
        raiseEvent(sinricpro, eventNames.powerState, device1, { state: 'On' });
      }, 2000);
    }, (err) => {
      console.error(err);
    },
    () => {
      if (interval) {
        clearInterval(interval);
        console.log('Disconnected');
      }
    });
    ```

## Usage

To run this example:

1.  Ensure you have Node.js installed.
2.  Install the SinricPro NodeJS SDK:
    ```bash
    npm install sinricpro
    ```
3.  Save the code as `switch_observable.js` (or any other `.js` file).
4.  Update the `appKey`, `secretKey`, `device1`, and `device2` with your actual SinricPro credentials and switch device IDs.
5.  Execute the file:
    ```bash
    node switch_observable.js
    ```

The application will connect to SinricPro, and you will see console logs when power state commands are received. Additionally, the example will periodically send `powerState` events to SinricPro.

## Code Snippet

```javascript
const { SinricPro, startSinricPro, raiseEvent, eventNames } = require('sinricpro');
const { startSinricProObservable } = require('sinricpro');

const appKey = '';
const secretKey = '';
const device1 = '';
const device2 = '';
const deviceId = [device1, device2];

function setPowerState(deviceid, data) {
  console.log(deviceid, data);
  return true;
}

const callbacks = {
  setPowerState,
};

const sinricpro = new SinricPro(appKey, deviceId, secretKey, true);

let interval = null;

// emit on connection is synched
startSinricProObservable(sinricpro, callbacks).subscribe((emit) => {
  console.log(emit);
  // const udp = new SinricProUdp(deviceId, secretKey);
  // udp.begin(callbacks);

  interval = setInterval(() => {
    raiseEvent(sinricpro, eventNames.powerState, device1, { state: 'On' });
  }, 2000);
}, (err) => {
  console.error(err);
},
() => {
  if (interval) {
    clearInterval(interval);
    console.log('Disconnected');
  }
});
```
