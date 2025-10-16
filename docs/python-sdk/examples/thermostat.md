## Thermostat Example
- **File:** `thermostat.py`
- **Description:** This example demonstrates how to control a smart thermostat using the SinricPro Python SDK. It supports setting the power state, target temperature, and thermostat mode (e.g., COOL, HEAT, AUTO).
- **Key Features:**
    - `SinricProConstants.SET_POWER_STATE`: Handles turning the thermostat on or off.
    - `SinricProConstants.TARGET_TEMPERATURE`: Sets the desired target temperature for the thermostat.
    - `SinricProConstants.SET_THERMOSTAT_MODE`: Sets the operating mode of the thermostat (e.g., `COOL`, `HEAT`, `AUTO`, `OFF`).
    - `SinricProConstants.CURRENT_TEMPERATURE`: (Commented out) An event that can be raised to report current temperature and humidity readings.
- **Usage:**
    1.  Replace `APP_KEY`, `APP_SECRET`, and `THERMOSTAT_ID` with your actual SinricPro credentials and device ID.
    2.  Run the script: `python thermostat.py`
    3.  **Complete Example (`thermostat.py`):**
        ```python
        from sinric import SinricPro, SinricProConstants
        import asyncio
        from asyncio import sleep

        APP_KEY = ""
        APP_SECRET = ""
        THERMOSTAT_ID = ""

        def power_state(device_id, state):
            print('device_id: {} state: {}'.format(device_id, state))
            return True, state

        def target_temperature(device_id, temperature):
            print('device_id: {} set temperature: {}'.format(device_id, temperature))
            return True, temperature

        def set_thermostate_mode(device_id, thermostat_mode):
            print('device_id: {} set thermostat mode: {}'.format(device_id, thermostat_mode))
            return True, thermostat_mode

        def mode_value(device_id, mode_value):
            print(device_id, mode_value)
            return True, mode_value

        async def events():
            while True:
                # client.event_handler.raise_event(THERMOSTAT_ID, SinricProConstants.SET_THERMOSTAT_MODE, data= { SinricProConstants.MODE : SinricProConstants.THERMOSTAT_MODE_COOL})
        # client.event_handler.raise_event(THERMOSTAT_ID, SinricProConstants.SET_POWER_STATE, data= {SinricProConstants.STATE: SinricProConstants.POWER_STATE_OFF})
        # client.event_handler.raise_event(THERMOSTAT_ID, SinricProConstants.CURRENT_TEMPERATURE, data={'humidity': 75.3, 'temperature': 24})
        # Server will trottle / block IPs sending events too often.
                await sleep(60)

        callbacks = {
            SinricProConstants.SET_POWER_STATE: power_state,
            SinricProConstants.TARGET_TEMPERATURE: target_temperature,
            SinricProConstants.SET_THERMOSTAT_MODE: set_thermostate_mode
        }

        if __name__ == '__main__':
            loop = asyncio.get_event_loop()
            client = SinricPro(APP_KEY, [THERMOSTAT_ID], callbacks, event_callbacks=events,
                               enable_log=True, restore_states=False, secret_key=APP_SECRET)
            loop.run_until_complete(client.connect())
        ```
    4.  **Controlling the Thermostat:**
        *   **From SinricPro App/Voice Assistant:** When you interact with the thermostat device via the SinricPro app or a connected voice assistant, the corresponding callback functions (`power_state`, `target_temperature`, or `set_thermostate_mode`) in your script will be invoked.
        *   **Simulating Events (from your code):** You can programmatically raise events to update the state on the SinricPro server or simulate commands by uncommenting the relevant lines in the `events()` function or directly in the `if __name__ == '__main__':` block.
    5.  Observe the `print` statements in the console for device state changes.
- **Code Snippet (Callbacks):**
    ```python
    def power_state(device_id, state):
        print('device_id: {} state: {}'.format(device_id, state))
        # Implement actual control of your thermostat hardware here
        return True, state

    def target_temperature(device_id, temperature):
        print('device_id: {} set temperature: {}'.format(device_id, temperature))
        # Implement actual control to set target temperature
        return True, temperature

    def set_thermostate_mode(device_id, thermostat_mode):
        print('device_id: {} set thermostat mode: {}'.format(device_id, thermostat_mode))
        # Implement actual control to set thermostat mode
        return True, thermostat_mode
    ```
