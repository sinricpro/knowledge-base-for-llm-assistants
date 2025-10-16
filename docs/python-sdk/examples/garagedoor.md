## Garage Door Example
- **File:** `garagedoor.py`
- **Description:** This example demonstrates how to control a garage door using the SinricPro Python SDK. It supports opening, closing, and stopping the garage door.
- **Key Features:**
    - `SinricProConstants.SET_POWER_STATE`: Handles turning the garage door on or off (often mapped to open/close).
    - `SinricProConstants.SET_MODE`: Used to set the garage door to specific modes like `OPEN` or `CLOSE`.
- **Usage:**
    1.  Replace `APP_KEY`, `APP_SECRET`, and `GARAGEDOOR_ID` with your actual SinricPro credentials and device ID.
    2.  Run the script: `python garagedoor.py`
    3.  **Complete Example (`garagedoor.py`):**
        ```python
        from sinric import SinricPro, SinricProConstants
        import asyncio

        APP_KEY = ''
        APP_SECRET = ''
        GARAGEDOOR_ID = ''


        def power_state(device_id, state):
            print('device_id: {} state: {}'.format(device_id, state))
            return True, device_id


        def set_mode(device_id, state, instance_id):
            print('device_id: {} mode: {}'.format(device_id, state))
            return True, state, instance_id


        callbacks = {
            SinricProConstants.SET_MODE: set_mode,
            SinricProConstants.SET_POWER_STATE: power_state
        }

        if __name__ == '__main__':
            loop = asyncio.get_event_loop()
            client = SinricPro(APP_KEY, [GARAGEDOOR_ID], callbacks,
                               enable_log=False, restore_states=False, secret_key=APP_SECRET)
            loop.run_until_complete(client.connect())

            # Example of simulating events (uncomment to use):
            # client.event_handler.raise_event(GARAGEDOOR_ID, SinricProConstants.SET_MODE, data = {SinricProConstants.MODE: SinricProConstants.OPEN })
            # client.event_handler.raise_event(GARAGEDOOR_ID, SinricProConstants.SET_MODE, data = {SinricProConstants.MODE: SinricProConstants.CLOSE })
        ```
    4.  **Controlling the Garage Door:**
        *   **From SinricPro App/Voice Assistant:** When you interact with the garage door device via the SinricPro app or a connected voice assistant, the corresponding callback functions (`power_state` or `set_mode`) in your script will be invoked.
        *   **Simulating Events (from your code):** You can programmatically raise events to update the state on the SinricPro server or simulate commands by uncommenting the relevant lines in the `if __name__ == '__main__':` block.
    5.  Observe the `print` statements in the console for device state changes.
- **Code Snippet (Callbacks):**
    ```python
    def power_state(device_id, state):
        print('device_id: {} state: {}'.format(device_id, state))
        # Implement actual control of your garage door hardware here
        return True, device_id # Return True and the device_id to acknowledge the command

    def set_mode(device_id, state, instance_id):
        print('device_id: {} mode: {}'.format(device_id, state))
        # Implement actual control to set the garage door mode
        return True, state, instance_id # Return True, the new mode, and instance_id
    ```
