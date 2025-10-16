## Smart Lock Example
- **File:** `smart_lock.py`
- **Description:** This example demonstrates how to control a smart lock using the SinricPro Python SDK. It supports locking and unlocking the device.
- **Key Features:**
    - `SinricProConstants.SET_LOCK_STATE`: Handles locking or unlocking the smart lock.
- **Usage:**
    1.  Replace `APP_KEY`, `APP_SECRET`, and `LOCK_ID` with your actual SinricPro credentials and device ID.
    2.  Run the script: `python smart_lock.py`
    3.  **Complete Example (`smart_lock.py`):**
        ```python
        from sinric import SinricPro, SinricProConstants
        import asyncio

        APP_KEY = ''
        APP_SECRET = ''
        LOCK_ID = ''


        def lock_state(device_id, state):
            print('device_id: {} lock state: {}'.format(device_id, state))
            return True, state


        callbacks = {
            SinricProConstants.SET_LOCK_STATE: lock_state
        }

        if __name__ == '__main__':
            loop = asyncio.get_event_loop()
            client = SinricPro(APP_KEY, [LOCK_ID], callbacks, enable_log=False,
                               restore_states=False, secret_key=APP_SECRET)
            loop.run_until_complete(client.connect())


        # Example of simulating events (uncomment to use):
        # client.event_handler.raise_event(LOCK_ID, SinricProConstants.SET_LOCK_STATE, data={
        #                                  SinricProConstants.STATE: SinricProConstants.LOCK_STATE_LOCKED})
        # client.event_handler.raise_event(LOCK_ID, SinricProConstants.SET_LOCK_STATE, data={
        #                                  SinricProConstants.STATE: SinricProConstants.LOCK_STATE_UNLOCKED})
        ```
    4.  **Controlling the Smart Lock:**
        *   **From SinricPro App/Voice Assistant:** When you lock or unlock the smart lock via the SinricPro app or a connected voice assistant, the `lock_state` callback in your script will be invoked.
        *   **Simulating Events (from your code):** You can programmatically raise events to update the state on the SinricPro server or simulate commands by uncommenting the relevant lines in the `if __name__ == '__main__':` block.
    5.  Observe the `print` statements in the console for device state changes.
- **Code Snippet (Callbacks):**
    ```python
    def lock_state(device_id, state):
        print('device_id: {} lock state: {}'.format(device_id, state))
        # Implement actual control of your smart lock hardware here
        return True, state # Return True and the new state to acknowledge the command
    ```
