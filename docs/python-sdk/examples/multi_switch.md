## Multi Switch Example
- **File:** `multi_switch.py`
- **Description:** This example demonstrates how to control multiple independent switches using a single SinricPro Python SDK instance. It shows how to handle power state changes for different device IDs within the same application.
- **Key Features:**
    - `SinricProConstants.SET_POWER_STATE`: Handles turning individual switches on or off based on their `device_id`.
- **Usage:**
    1.  Replace `APP_KEY`, `APP_SECRET`, `SWITCH_ID_1`, and `SWITCH_ID_2` with your actual SinricPro credentials and device IDs for each switch.
    2.  Run the script: `python multi_switch.py`
    3.  **Complete Example (`multi_switch.py`):**
        ```python
        from sinric import SinricPro, SinricProConstants
        import asyncio

        APP_KEY = ''
        APP_SECRET = ''
        SWITCH_ID_1 = ''
        SWITCH_ID_2 = ''


        def power_state(device_id, state):
            if device_id == SWITCH_ID_1:
                print('device_id: {} state: {}'.format(device_id, state))
            elif device_id == SWITCH_ID_2:
                print('device_id: {} state: {}'.format(device_id, state))
            else:
                print("device_id not found!")

            return True, state


        callbacks = {
            SinricProConstants.SET_POWER_STATE: power_state
        }

        if __name__ == '__main__':
            loop = asyncio.get_event_loop()
            client = SinricPro(APP_KEY, [SWITCH_ID_1, SWITCH_ID_2], callbacks,
                               enable_log=False, restore_states=False, secret_key=APP_SECRET)
            loop.run_until_complete(client.connect())

            # Example of simulating events (uncomment to use):
            # client.event_handler.raise_event(SWITCH_ID, SinricProConstants.SET_POWER_STATE, data = {SinricProConstants.STATE: SinricProConstants.POWER_STATE_ON })
            # client.event_handler.raise_event(SWITCH_ID, SinricProConstants.SET_POWER_STATE, data = {SinricProConstants.STATE: SinricProConstants.POWER_STATE_OFF })
        ```
    4.  **Controlling the Switches:**
        *   **From SinricPro App/Voice Assistant:** When you interact with either switch via the SinricPro app or a connected voice assistant, the `power_state` callback will be invoked with the `device_id` of the specific switch being controlled.
        *   **Simulating Events (from your code):** You can programmatically raise events to update the state on the SinricPro server or simulate commands for each switch by uncommenting the relevant lines in the `if __name__ == '__main__':` block.
    5.  Observe the `print` statements in the console for device state changes, which will indicate which switch was controlled.
- **Code Snippet (Callbacks):**
    ```python
    def power_state(device_id, state):
        # Implement logic to control the specific switch based on device_id
        if device_id == SWITCH_ID_1:
            print('Controlling Switch 1: state: {}'.format(state))
            # Control hardware for SWITCH_ID_1
        elif device_id == SWITCH_ID_2:
            print('Controlling Switch 2: state: {}'.format(state))
            # Control hardware for SWITCH_ID_2
        else:
            print("device_id not found!")
        return True, state # Return True and the new state to acknowledge the command
    ```
