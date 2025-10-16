## Custom Device Example
- **File:** `custom_device.py`
- **Description:** This example demonstrates how to create a custom device in SinricPro that supports basic power control, range adjustments, and mode settings. It serves as a template for devices that don't fit standard categories but require these common functionalities.
- **Key Features:**
    - `SinricProConstants.SET_POWER_STATE`: Handles turning the device on or off.
    - `SinricProConstants.SET_RANGE_VALUE`: Sets a numerical range value for the device.
    - `SinricProConstants.SET_MODE`: Sets a specific mode for the device.
- **Usage:**
    1.  Replace `APP_KEY`, `APP_SECRET`, and `DEVICE_ID` with your actual SinricPro credentials and the custom device ID.
    2.  Run the script: `python custom_device.py`
    3.  **Complete Example (`custom_device.py`):**
        ```python
        from sinric import SinricPro, SinricProConstants
        import asyncio

        APP_KEY = ''
        APP_SECRET = ''
        DEVICE_ID = ''


        def power_state(device_id, state):
            print(device_id, state)
            return True, state


        def range_value(device_id, range_value, instance_id):
            print(device_id, range_value, instance_id)
            return True, range_value, instance_id


        def mode_value(device_id, mode_value, instance_id):
            print(device_id, mode_value, instance_id)
            return True, mode_value, instance_id


        callbacks = {
            SinricProConstants.SET_POWER_STATE: power_state,
            SinricProConstants.SET_RANGE_VALUE: range_value,
            SinricProConstants.SET_MODE: mode_value
        }

        if __name__ == '__main__':
            loop = asyncio.get_event_loop()
            client = SinricPro(APP_KEY, [DEVICE_ID], callbacks,
                               enable_log=False, restore_states=False, secret_key=APP_SECRET)
            loop.run_until_complete(client.connect())

            # Example of simulating events (uncomment to use):
            # client.event_handler.raise_event(DEVICE_ID, SinricProConstants.SET_POWER_STATE, data = {SinricProConstants.STATE: SinricProConstants.POWER_STATE_ON })
            # client.event_handler.raise_event(DEVICE_ID, SinricProConstants.SET_RANGE_VALUE, data = {SinricProConstants.RANGE_VALUE: 50 })
            # client.event_handler.raise_event(DEVICE_ID, SinricProConstants.SET_MODE, data = {SinricProConstants.MODE: "MODE1" })
        ```
    4.  **Controlling the Custom Device:**
        *   **From SinricPro App/Voice Assistant:** When you interact with your custom device via the SinricPro app or a connected voice assistant, the corresponding callback functions (`power_state`, `range_value`, or `mode_value`) in your script will be invoked.
        *   **Simulating Events (from your code):** You can programmatically raise events to update the state on the SinricPro server or simulate commands by uncommenting the relevant lines in the `if __name__ == '__main__':` block.
    5.  Observe the `print` statements in the console for device state changes.
- **Code Snippet (Callbacks):**
    ```python
    def power_state(device_id, state):
        print(device_id, state)
        # Implement actual control of your custom device hardware here
        return True, state # Return True and the new state to acknowledge the command

    def range_value(device_id, range_value, instance_id):
        print(device_id, range_value, instance_id)
        # Implement actual control to set the range value
        return True, range_value, instance_id # Return True, the new value, and instance_id

    def mode_value(device_id, mode_value, instance_id):
        print(device_id, mode_value, instance_id)
        # Implement actual control to set the mode
        return True, mode_value, instance_id # Return True, the new mode, and instance_id
    ```
