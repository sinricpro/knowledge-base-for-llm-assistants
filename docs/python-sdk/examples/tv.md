## TV Example
- **File:** `tv.py`
- **Description:** This example demonstrates how to control a smart TV using the SinricPro Python SDK. It covers power state, volume control, media playback controls, input selection, and channel changing.
- **Key Features:**
    - `SinricProConstants.SET_POWER_STATE`: Handles turning the TV on or off.
    - `SinricProConstants.SET_VOLUME`: Sets the volume level of the TV.
    - `SinricProConstants.ADJUST_VOLUME`: Adjusts the volume relative to the current level.
    - `SinricProConstants.MEDIA_CONTROL`: Controls media playback (e.g., Play, Pause, Stop, FastForward, Rewind).
    - `SinricProConstants.SELECT_INPUT`: Selects a specific input source (e.g., HDMI1, AV).
    - `SinricProConstants.CHANGE_CHANNEL`: Changes the TV channel by name.
    - `SinricProConstants.SKIP_CHANNELS`: Skips channels by a specified count (e.g., +1, -1).
- **Usage:**
    1.  Replace `APP_KEY`, `APP_SECRET`, and `TV_ID` with your actual SinricPro credentials and device ID.
    2.  Run the script: `python tv.py`
    3.  **Complete Example (`tv.py`):**
        ```python
        from sinric import SinricPro, SinricProConstants
        import asyncio

        APP_KEY = ''
        APP_SECRET = ''
        TV_ID = ''


        def power_state(device_id, state):
            print('state : ', state)
            return True, state


        def set_volume(device_id, volume):
            print('volume : ', volume)
            return True, volume


        def adjust_volume(device_id, volume):
            print('volume : ', volume)
            return True, volume


        def media_control(device_id, control):
            print('control : ', control)
            return True, control


        def select_input(device_id, input):
            print('input : ', input)
            return True, input


        def change_channel(device_id, channel_name):
            print('channel_name : ', channel_name)
            return True, channel_name


        def skip_channels(device_id, channel_count):
            print('channel_count : ', channel_count)
            return True, channel_count


        callbacks = {
            SinricProConstants.SET_POWER_STATE: power_state,
            SinricProConstants.SET_VOLUME: set_volume,
            SinricProConstants.ADJUST_VOLUME: adjust_volume,
            SinricProConstants.MEDIA_CONTROL: media_control,
            SinricProConstants.SELECT_INPUT: select_input,
            SinricProConstants.CHANGE_CHANNEL: change_channel,
            SinricProConstants.SKIP_CHANNELS: skip_channels
        }

        if __name__ == '__main__':
            loop = asyncio.get_event_loop()
            client = SinricPro(APP_KEY, [TV_ID], callbacks, enable_log=False,
                               restore_states=False, secret_key=APP_SECRET)
            loop.run_until_complete(client.connect())

            # Example of simulating events (uncomment to use):
            # client.event_handler.raise_event(TV_ID, SinricProConstants.SET_VOLUME, data={'volume': 0})
            # client.event_handler.raise_event(TV_ID, SinricProConstants.MEDIA_CONTROL, data={'control': 'FastForward'})
            # client.event_handler.raise_event(TV_ID, SinricProConstants.CHANGE_CHANNEL, data={'name': 'HBO'})
            # client.event_handler.raise_event(TV_ID, SinricProConstants.SELECT_INPUT, data={"input":"HDMI"})
        ```
    4.  **Controlling the TV:**
        *   **From SinricPro App/Voice Assistant:** When you interact with the TV device via the SinricPro app or a connected voice assistant, the corresponding callback functions (e.g., `power_state`, `set_volume`, `media_control`) in your script will be invoked.
        *   **Simulating Events (from your code):** You can programmatically raise events to update the state on the SinricPro server or simulate commands by uncommenting the relevant lines in the `if __name__ == '__main__':` block.
    5.  Observe the `print` statements in the console for device state changes.
- **Code Snippet (Callbacks):**
    ```python
    def power_state(device_id, state):
        print('state : ', state)
        # Implement actual control of your TV hardware here
        return True, state

    def set_volume(device_id, volume):
        print('volume : ', volume)
        # Implement actual control to set volume
        return True, volume

    def adjust_volume(device_id, volume):
        print('volume : ', volume)
        # Implement actual control to adjust volume
        return True, volume

    def media_control(device_id, control):
        print('control : ', control)
        # Implement actual control for media playback
        return True, control

    def select_input(device_id, input):
        print('input : ', input)
        # Implement actual control to select input
        return True, input

    def change_channel(device_id, channel_name):
        print('channel_name : ', channel_name)
        # Implement actual control to change channel
        return True, channel_name

    def skip_channels(device_id, channel_count):
        print('channel_count : ', channel_count)
        # Implement actual control to skip channels
        return True, channel_count
    ```
