# Camera Example

This example demonstrates how to integrate a camera device with SinricPro, enabling functionalities like power control and providing camera streams via WebRTC or direct URLs (RTSP/HLS). It showcases how to handle requests for camera streams from platforms like Alexa Echo and Google Home.

## Key Features

-   **WebRTC Integration:** Implements `getWebRTCAnswer` to handle WebRTC offers from voice assistants (e.g., Alexa Echo) and return a WebRTC answer, facilitating real-time video streaming.
-   **Camera Stream URL Provision:** Implements `getCameraStreamUrl` to provide direct RTSP or HLS stream URLs based on the requested protocol, suitable for platforms like Google Home.
-   **Power State Control:** Includes a `setPowerState` callback to manage the camera's power state.
-   **Connection Management:** Includes `onDisconnect` and `onConnected` callbacks to handle the WebSocket connection status with SinricPro.

## Setup

1.  **Obtain Credentials:**
    *   Replace `APPKEY` with your SinricPro App Key.
    *   Replace `APPSECRET` with your SinricPro Secret Key.
    *   Replace `cameraId` with the Device ID of your camera device.

    ```javascript
    const APPKEY =  ""; // YOUR_APP_KEY
    const APPSECRET = ""; // YOUR_SECRET_KEY
    const cameraId = ""; // YOUR_CAMERA_DEVICE_ID
    const deviceIds = [cameraId];
    ```

2.  **WebRTC Answer Implementation (`mediamtx`):**
    The `mediamtx` function is a placeholder for your WebRTC signaling server logic. In a real-world scenario, this function would communicate with your media server (e.g., MediaMTX, Janus, WebRTC SFU) to process the SDP offer and generate an SDP answer.

    ```javascript
    async function mediamtx(offer) {
      /* 
        Get the answer from mediamtx `http://<hostname>:8889/<name>/whep`. eg: http://pi3:8889/cam/whep
      */
      const url = `http://pi3:8889/cam/whep`; 
      const response = await fetch(url, {
        headers: {
          "content-type": "application/sdp",
        },
        body: offer,
        method: "POST",
      });
      return await response.text();   
    }
    ```

3.  **Camera Stream URL Implementation:**
    The `getCameraStreamUrl` function should return the actual stream URL for your camera based on the requested protocol (`hls` or `rtsp`). The example provides placeholder URLs.

    ```javascript
    const getCameraStreamUrl = async (deviceid, cameraStreamProtocol) => {
      if(cameraStreamProtocol === 'hls') {
        return { success: true, url: 'https://cph-p2p-msl.akamaized.net/hls/live/2000341/test/master.m3u8' };
      } 
      else if(cameraStreamProtocol === 'rtsp') {
        return { success: true, url: 'rtsp://rtspurl:443' };
      } else {
        return { success: false};
      }
    };
    ```

4.  **Initialize and Start SinricPro:**
    The `SinricPro` instance is initialized with your credentials and device IDs. The `startSinricPro` function then establishes the connection and registers your callback functions.

    ```javascript
    const sinricpro = new SinricPro(APPKEY, deviceIds, APPSECRET, false);
    const callbacks = { setPowerState, getWebRTCAnswer, getCameraStreamUrl, onDisconnect, onConnected };
    startSinricPro(sinricpro, callbacks);
    ```

## Usage

To run this example:

1.  Ensure you have Node.js installed.
2.  Install the SinricPro NodeJS SDK and `node-fetch`:
    ```bash
    npm install sinricpro node-fetch
    ```
3.  Save the code as `camera.js` (or any other `.js` file).
4.  Update the `APPKEY`, `APPSECRET`, and `cameraId` with your actual SinricPro credentials and device ID.
5.  Implement your `mediamtx` function to interact with your media server.
6.  Update the `getCameraStreamUrl` function with your actual camera stream URLs.
7.  Execute the file:
    ```bash
    node camera.js
    ```

The application will connect to SinricPro, and you can then interact with your camera device via voice assistants or the SinricPro app.

## Code Snippet

```javascript
const {
  SinricPro, startSinricPro, raiseEvent, eventNames,
} = require('sinricpro');

const fetch = require('node-fetch');

const APPKEY =  "";
const APPSECRET = "";
const cameraId = "";
const deviceIds = [cameraId];

async function mediamtx(offer) {
  /* 
    Get the answer from mediamtx `http://<hostname>:8889/<name>/whep`. eg: http://pi3:8889/cam/whep
  */
  const url = `http://pi3:8889/cam/whep`; 
  const response = await fetch(url, {
    headers: {
      "content-type": "application/sdp",
    },
    body: offer,
    method: "POST",
  });
  return await response.text();   
}

const getWebRTCAnswer = async (deviceid, base64Offer) => {
  // Alexa Eco needs camera stream answer
  const offer = Buffer.from(base64Offer, 'base64').toString();
  try {
    const answer = await mediamtx(offer);
    const answerInBase64 = Buffer.from(answer).toString('base64');
    return { success: true, answer: answerInBase64 };
  } catch (error) {
    console.error(error);
    return { success: false };
  }  
};
 
const getCameraStreamUrl = async (deviceid, cameraStreamProtocol) => {
  // Google Home: RTSP protocol not supported. Requires a Chromecast TV or Google Nest Hub
  // Alexa: RTSP url must be interleaved TCP on port 443 (for both RTP and RTSP) over TLS 1.2 port 443

  if(cameraStreamProtocol === 'hls') {
    return { success: true, url: 'https://cph-p2p-msl.akamaized.net/hls/live/2000341/test/master.m3u8' };
  } 
  else if(cameraStreamProtocol === 'rtsp') {
    return { success: true, url: 'rtsp://rtspurl:443' };
  } else {
    return { success: false};
  }
};

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

const sinricpro = new SinricPro(APPKEY, deviceIds, APPSECRET, false);
const callbacks = { setPowerState, getWebRTCAnswer, getCameraStreamUrl, onDisconnect, onConnected };

startSinricPro(sinricpro, callbacks);
```
