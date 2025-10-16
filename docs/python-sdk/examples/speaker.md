## Speaker Example
- **File:** `speaker.py`
- **Description:** This example demonstrates how to control a smart speaker using the SinricPro Python SDK. It covers power state, volume control, muting, and equalizer band adjustments.
- **Key Features:**
    - `SinricProConstants.SET_POWER_STATE`: Handles turning the speaker on or off.
    - `SinricProConstants.SET_VOLUME`: Sets the volume level of the speaker.
    - `SinricProConstants.ADJUST_VOLUME`: Adjusts the volume relative to the current level.
    - `SinricProConstants.SET_MUTE`: Mutes or unmutes the speaker.
    - `SinricProConstants.SET_BANDS`: Sets the level of specific equalizer bands.
    - `SinricProConstants.ADJUST_BANDS`: Adjusts the level of specific equalizer bands relative to the current level.
    - `SinricProConstants.RESET_BANDS`: Resets equalizer bands to their default levels.
    - `SinricProConstants.SET_MODE`: Sets a specific mode for the speaker (e.g., "MUSIC", "MOVIE").
- **Usage:**
    1.  Replace `APP_KEY`, `APP_SECRET`, and `SPEAKER_ID` with your actual SinricPro credentials and device ID.
    2.  Run the script: `python speaker.py`
    3.  **Complete Example (`speaker.py`):**
        ```python
        from sinric import SinricPro, SinricProConstants
        import asyncio

        APP_KEY = ''
        APP_SECRET = ''
        SPEAKER_ID = ''


        def power_state(device_id, state):
            print('device_id: {} lock state: {}'.format(device_id, state))
            return True, state


        def set_bands(device_id, name, level):
            print('device_id: {}, name: {}, name: {}'.format(device_id, name, level))
            return True, {'name': name, 'level': level}


        def adjust_bands(device_id, name, level, direction):
            print('device_id: {}, name: {}, name: {}, direction: {}'.format(
                device_id, name, level, direction))
            return True, {'name': name, 'level': level}


        def reset_bands(device_id, band1, band2, band3):
            print('device_id: {}, band1: {}, band2: {}, band3: {}'.format(
                device_id, band1, band2, band3))
            return True


        def set_mode(device_id, mode):
            print('device_id: {} mode: {}'.format(device_id, mode))
            return True, mode


        def set_mute(device_id, mute):
            print('device_id: {} mute: {}'.format(device_id, mute))
            return True, mute


        def set_volume(device_id, volume):
            print('device_id: {} volume: {}'.format(device_id, volume))
            return True, volume


        callbacks = {
            SinricProConstants.SET_POWER_STATE: power_state,
            SinricProConstants.SET_BANDS: set_bands,
            SinricProConstants.SET_MODE: set_mode,
            SinricProConstants.ADJUST_BANDS: adjust_bands,
            SinricProConstants.RESET_BANDS: reset_bands,
            SinricProConstants.SET_MUTE: set_mute,
            SinricProConstants.SET_VOLUME: set_volume
        }

        if __name__ == '__main__':
            loop = asyncio.get_event_loop()
            client = SinricPro(APP_KEY, [SPEAKER_ID], callbacks,
                               enable_log=False, restore_states=False, secret_key=APP_SECRET)
            loop.run_until_complete(client.connect())

        # Example of simulating events (uncomment to use):
        # client.event_handler.raise_event(speakerId, SinricProConstants.SET_BANDS, data = {'name': '','level': 0})
        ```
    4.  **Controlling the Speaker:**
        *   **From SinricPro App/Voice Assistant:** When you interact with the speaker device via the SinricPro app or a connected voice assistant, the corresponding callback functions (e.g., `power_state`, `set_volume`, `set_mute`) in your script will be invoked.
        *   **Simulating Events (from your code):** You can programmatically raise events to update the state on the SinricPro server or simulate commands by uncommenting the relevant lines in the `if __name__ == '__main__':` block.
    5.  Observe the `print` statements in the console for device state changes.
- **Code Snippet (Callbacks):**
    ```python
    def power_state(device_id, state):
        print('device_id: {} power state: {}'.format(device_id, state))
        # Implement actual control of your speaker hardware here
        return True, state

    def set_volume(device_id, volume):
        print('device_id: {} volume: {}'.format(device_id, volume))
        # Implement actual control to set volume
        return True, volume

    def adjust_volume(device_id, volume):
        print('device_id: {} adjusted volume: {}'.format(device_id, volume))
        # Implement actual control to adjust volume
        return True, volume

    def set_mute(device_id, mute):
        print('device_id: {} mute: {}'.format(device_id, mute))
        # Implement actual control to mute/unmute
        return True, mute

    def set_bands(device_id, name, level):
        print('device_id: {}, band name: {}, level: {}'.format(device_id, name, level))
        # Implement actual control to set equalizer bands
        return True, {'name': name, 'level': level}

    def adjust_bands(device_id, name, level, direction):
        print('device_id: {}, band name: {}, level: {}, direction: {}'.format(
            device_id, name, level, direction))
        # Implement actual control to adjust equalizer bands
        return True, {'name': name, 'level': level}

    def reset_bands(device_id, band1, band2, band3):
        print('device_id: {}, band1: {}, band2: {}, band3: {}'.format(
            device_id, band1, band2, band3))
        # Implement actual control to reset equalizer bands
        return True

    def set_mode(device_id, mode):
        print('device_id: {} mode: {}'.format(device_id, mode))
        # Implement actual control to set speaker mode
        return True, mode
    ```
