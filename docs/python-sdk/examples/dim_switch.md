## Dim Switch Example
- **File:** `dim_switch.py`
- **Description:** This example demonstrates how to implement a dimmable switch using the SinricPro Python SDK. It supports turning the switch on/off and setting its brightness level.
- **Key Features:**
    - `SinricProConstants.SET_POWER_STATE`: Handles turning the dim switch on or off.
    - `SinricProConstants.SET_POWER_LEVEL`: Sets the brightness level of the dim switch (e.g., 0-100%).
- **Usage:**
    1.  Replace `APP_KEY`, `APP_SECRET`, and `DIM_SWITCH_ID` with your actual SinricPro credentials and device ID.
    2.  Run the script: `python dim_switch.py`
    3.  **Complete Example (`dim_switch.py`):**
        ```python
        from sinric import SinricPro, SinricProConstants
        import asyncio

        APP_KEY = ''
        APP_SECRET = ''
        DIM_SWITCH_ID = ''


        def power_state(device_id, state):
            print('device_id: {} state: {}'.format(device_id, state))
            return True, state


        def power_level(device_id, power_level):
            print('device_id: {} power level: {}'.format(device_id, power_level))
            return True, power_level


        callbacks = {
            SinricProConstants.SET_POWER_STATE: power_state,
            SinricProConstants.SET_POWER_LEVEL: power_level
        }

        if __name__ == '__main__':
            loop = asyncio.get_event_loop()
            client = SinricPro(APP_KEY, [DIM_SWITCH_ID], callbacks,
                               enable_log=False, restore_states=False, secret_key=APP_SECRET)
            loop.run_until_complete(client.connect())

            # Example of simulating events (uncomment to use):
            # client.event_handler.raise_event(DIM_SWITCH_ID, SinricProConstants.SET_POWER_LEVEL, data = {SinricProConstants.POWER_LEVEL: 50 })
            # client.event_handler.raise_event(DIM_SWITCH_ID, SinricProConstants.SET_POWER_STATE, data = {SinricProConstants.STATE: SinricProConstants.POWER_STATE_ON })
            # client.event_handler.raise_event(DIM_SWITCH_ID, SinricProConstants.SET_POWER_STATE, data = {SinricProConstants.STATE: SinricProConstants.POWER_STATE_OFF })
        ```
    4.  **Controlling the Dim Switch:**
        *   **From SinricPro App/Voice Assistant:** When you change the power state or brightness level of the dim switch via the SinricPro app or a connected voice assistant, the corresponding callback functions (`power_state` or `power_level`) in your script will be invoked.
        *   **Simulating Events (from your code):** You can programmatically raise events to update the state on the SinricPro server or simulate commands by uncommenting the relevant lines in the `if __name__ == '__main__':` block.
    5.  Observe the `print` statements in the console for device state changes.
- **Code Snippet (Callbacks):**
    ```python
    def power_state(device_id, state):
        print('device_id: {} state: {}'.format(device_id, state))
        # Implement actual control of your dim switch hardware here
        return True, state # Return True and the new state to acknowledge the command

    def power_level(device_id, power_level):
        print('device_id: {} power level: {}'.format(device_id, power_level))
        # Implement actual control to set the brightness level
        return True, power_level # Return True and the new power level
    ```
