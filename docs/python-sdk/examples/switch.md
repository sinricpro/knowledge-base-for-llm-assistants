## Switch Example
- **File:** `switch.py`
- **Description:** This example demonstrates a basic on/off switch implementation using the SinricPro Python SDK. It shows how to handle power state changes for a single switch device.
- **Key Features:**
    - `SinricProConstants.SET_POWER_STATE`: Handles turning the switch on or off.
- **Usage:**
    1.  Replace `APP_KEY`, `APP_SECRET`, and `SWITCH_ID` with your actual SinricPro credentials and device ID.
    2.  Run the script: `python switch.py`
    3.  **Complete Example (`switch.py`):**
        ```python
        from sinric import SinricPro, SinricProConstants
        import asyncio

        APP_KEY = ''
        APP_SECRET = ''
        SWITCH_ID = ''


        def power_state(device_id, state):
            print('device_id: {} state: {}'.format(device_id, state))
            return True, state


        callbacks = {
            SinricProConstants.SET_POWER_STATE: power_state
        }

        if __name__ == '__main__':
            loop = asyncio.get_event_loop()
            client = SinricPro(APP_KEY, [SWITCH_ID], callbacks,
                               enable_log=False, restore_states=False, secret_key=APP_SECRET)
            loop.run_until_complete(client.connect())

            # Example of simulating events (uncomment to use):
            # client.event_handler.raise_event(SWITCH_ID, SinricProConstants.SET_POWER_STATE, data = {SinricProConstants.STATE: SinricProConstants.POWER_STATE_ON })
            # client.event_handler.raise_event(SWITCH_ID, SinricProConstants.SET_POWER_STATE, data = {SinricProConstants.STATE: SinricProConstants.POWER_STATE_OFF })
        ```
    4.  **Controlling the Switch:**
        *   **From SinricPro App/Voice Assistant:** When you turn the switch on or off via the SinricPro app or a connected voice assistant, the `power_state` callback in your script will be invoked.
        *   **Simulating Events (from your code):** You can programmatically raise events to update the state on the SinricPro server or simulate commands by uncommenting the relevant lines in the `if __name__ == '__main__':` block.
    5.  Observe the `print` statements in the console for device state changes.
- **Code Snippet (Callbacks):**
    ```python
    def power_state(device_id, state):
        print('device_id: {} state: {}'.format(device_id, state))
        # Implement actual control of your switch hardware here
        return True, state # Return True and the new state to acknowledge the command
    ```
