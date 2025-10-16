## Camera Example
- **File:** `camera.py`
- **Description:** This example demonstrates how to integrate a camera device with SinricPro, supporting WebRTC and stream URL retrieval. It shows how to handle requests for WebRTC answers and provide camera stream URLs (RTSP/HLS).
- **Key Features:**
    - `SinricProConstants.GET_WEBRTC_ANSWER`: Provides a WebRTC answer based on an offer, typically interacting with a media server like MediaMTX.
    - `SinricProConstants.GET_CAMERA_STREAM_URL`: Returns the camera stream URL (RTSP or HLS) based on the requested protocol.
    - `SinricProConstants.SET_POWER_STATE`: Handles turning the camera on or off.
- **Usage:**
    1.  Replace `APP_KEY`, `APP_SECRET`, and `CAMERA_ID` with your actual SinricPro credentials and device ID.
    2.  **Important:** Configure `mediamtx_url` in `get_webrtc_answer` to point to your MediaMTX server.
    3.  **Important:** Update the RTSP/HLS URLs in `get_camera_stream_url` to your actual camera stream URLs.
    4.  Run the script: `python camera.py`
    5.  **Complete Example (`camera.py`):**
        ```python
        from sinric import SinricPro, SinricProConstants
        import requests
        import asyncio
        import base64

        APP_KEY = ""
        APP_SECRET = ""
        CAMERA_ID = ''

        def get_webrtc_answer(device_id, offer):
            sdp_offer = base64.b64decode(offer)
            print('device_id: {} offer: {}'.format(device_id, offer))

            # PORT 8889 for WebRTC. eg: for PiCam, use http://<mediamtx-hostname>:8889/cam/whep
            mediamtx_url = "http://<mediamtx-hostname>:8889/<device>/whep"
            headers = {"Content-Type": "application/sdp"}
            response = requests.post(mediamtx_url, headers=headers, data=sdp_offer)

            if response.status_code == 201:
                answer = base64.b64encode(response.content).decode("utf-8")
                return True, answer
            else:
                return False


        def power_state(device_id, state):
            print('device_id: {} power state: {}'.format(device_id, state))
            return True, state

        def get_camera_stream_url(device_id, protocol):
            # Google Home: RTSP protocol not supported. Requires a Chromecast TV or Google Nest Hub
            # Alexa: RTSP url must be interleaved TCP on port 443 (for both RTP and RTSP) over TLS 1.2 port 443

            print('device_id: {} protocol: {}'.format(device_id, protocol))

            if protocol == "rstp":
                return True, 'rtsp://rtspurl:443'   # RSTP. 
            else: 
                return True, 'https://cph-p2p-msl.akamaized.net/hls/live/2000341/test/master.m3u8' # HLS

        callbacks = {
            SinricProConstants.GET_WEBRTC_ANSWER: get_webrtc_answer,
            SinricProConstants.GET_CAMERA_STREAM_URL: get_camera_stream_url,
            SinricProConstants.SET_POWER_STATE: power_state
        }

        if __name__ == '__main__':
            loop = asyncio.get_event_loop()
            client = SinricPro(APP_KEY, [CAMERA_ID], callbacks,
                               enable_log=False, restore_states=False, secret_key=APP_SECRET)
            loop.run_until_complete(client.connect())

            # Example of simulating events (uncomment to use):
            # client.event_handler.raise_event(CAMERA_ID, SinricProConstants.SET_POWER_STATE, data = {SinricProConstants.STATE: SinricProConstants.POWER_STATE_ON })
            # client.event_handler.raise_event(CAMERA_ID, SinricProConstants.SET_POWER_STATE, data = {SinricProConstants.STATE: SinricProConstants.POWER_STATE_OFF })
        ```
    6.  **Controlling the Camera:**
        *   **From SinricPro App/Voice Assistant:** When you interact with the camera device via the SinricPro app or a connected voice assistant, the corresponding callback functions (`power_state`, `get_webrtc_answer`, or `get_camera_stream_url`) in your script will be invoked.
        *   **Simulating Events (from your code):** While this example primarily focuses on handling incoming requests, you can simulate power state changes if needed by uncommenting the relevant lines in the `if __name__ == '__main__':` block.
    7.  Observe the `print` statements in the console for device state changes and stream requests.
- **Code Snippet (Callbacks):**
    ```python
    def get_webrtc_answer(device_id, offer):
        print('device_id: {} offer: {}'.format(device_id, offer))
        # Implement logic to generate WebRTC answer from your camera/media server
        # For example, interact with MediaMTX as shown in the original example
        return True, "YOUR_WEBRTC_ANSWER_BASE64" # Return True and the base64 encoded answer

    def power_state(device_id, state):
        print('device_id: {} power state: {}'.format(device_id, state))
        # Implement actual control of your camera hardware here
        return True, state # Return True and the new state to acknowledge the command

    def get_camera_stream_url(device_id, protocol):
        print('device_id: {} protocol: {}'.format(device_id, protocol))
        # Implement logic to return the appropriate stream URL based on protocol
        if protocol == "rstp":
            return True, 'rtsp://your_rtsp_url:443'
        else: # Assuming HLS or other
            return True, 'https://your_hls_url/master.m3u8'
    ```
