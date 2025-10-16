## Light Example
- **File:** `light.py`
- **Description:** This comprehensive example demonstrates how to control a smart light with various functionalities using the SinricPro Python SDK. It covers power state, brightness control, color setting (RGB), and color temperature adjustments.
- **Key Features:**
    - `SinricProConstants.SET_POWER_STATE`: Handles turning the light on or off.
    - `SinricProConstants.SET_BRIGHTNESS`: Sets the brightness level of the light (0-100%).
    - `SinricProConstants.ADJUST_BRIGHTNESS`: Adjusts the brightness relative to the current level.
    - `SinricProConstants.SET_COLOR`: Sets the color of the light using RGB values.
    - `SinricProConstants.SET_COLOR_TEMPERATURE`: Sets the color temperature of the light.
    - `SinricProConstants.INCREASE_COLOR_TEMPERATURE`: Increases the color temperature.
    - `SinricProConstants.DECREASE_COLOR_TEMPERATURE`: Decreases the color temperature.
- **Usage:**
    1.  Replace `APP_KEY`, `APP_SECRET`, and `LIGHT_ID` with your actual SinricPro credentials and device ID.
    2.  Run the script: `python light.py`
    3.  **Complete Example (`light.py`):**
        ```python
        from sinric import SinricPro, SinricProConstants
        import asyncio

        APP_KEY = ''
        APP_SECRET = ''
        LIGHT_ID = ''


        def power_state(device_id, state):
            print('device_id: {} state: {}'.format(device_id, state))
            return True, state


        def set_brightness(device_id, brightness):
            print('device_id: {} brightness: {}'.format(device_id, brightness))
            return True, brightness


        def adjust_brightness(device_id, brightness):
            print('device_id: {} adjusted brightness level: {}'.format(device_id, brightness))
            return True, brightness


        def set_color(device_id, r, g, b):
            print('device_id: {} R:{},G:{},B:{}'.format(device_id, r, g, b))
            return True


        def set_color_temperature(device_id, color_temperature):
            print('device_id: {} color temperature:{}'.format(
                device_id, color_temperature))
            return True


        def increase_color_temperature(device_id, value):
            print('device_id: {} value:{}'.format(device_id, value))
            return True, value


        def decrease_color_temperature(device_id, value):
            print('device_id: {} value:{}'.format(device_id, value))
            return True, value


        callbacks = {
            SinricProConstants.SET_POWER_STATE: power_state,
            SinricProConstants.SET_BRIGHTNESS: set_brightness,
            SinricProConstants.ADJUST_BRIGHTNESS: adjust_brightness,
            SinricProConstants.SET_COLOR: set_color,
            SinricProConstants.SET_COLOR_TEMPERATURE: set_color_temperature,
            SinricProConstants.INCREASE_COLOR_TEMPERATURE: increase_color_temperature,
            SinricProConstants.DECREASE_COLOR_TEMPERATURE: decrease_color_temperature
        }

        if __name__ == '__main__':
            loop = asyncio.get_event_loop()
            client = SinricPro(APP_KEY, [LIGHT_ID], callbacks, enable_log=False,
                               restore_states=False, secret_key=APP_SECRET)
            loop.run_until_complete(client.connect())

            # Example of simulating events (uncomment to use):
            # client.event_handler.raise_event(LIGHT_ID, SinricProConstants.SET_POWER_STATE, data = {SinricProConstants.STATE: SinricProConstants.POWER_STATE_ON })
            # client.event_handler.raise_event(LIGHT_ID, SinricProConstants.SET_POWER_STATE, data = {SinricProConstants.STATE: SinricProConstants.POWER_STATE_OFF })
            # client.event_handler.raise_event(LIGHT_ID, SinricProConstants.SET_COLOR, data = {SinricProConstants.RED: 0, SinricProConstants.GREEN: 0, SinricProConstants.BLUE: 0})
            # client.event_handler.raise_event(LIGHT_ID, SinricProConstants.SET_COLOR_TEMPERATURE, data={SinricProConstants.COLOR_TEMPERATURE: 2400})
        ```
    4.  **Controlling the Light:**
        *   **From SinricPro App/Voice Assistant:** When you interact with the light device via the SinricPro app or a connected voice assistant, the corresponding callback functions (e.g., `power_state`, `set_brightness`, `set_color`) in your script will be invoked.
        *   **Simulating Events (from your code):** You can programmatically raise events to update the state on the SinricPro server or simulate commands by uncommenting the relevant lines in the `if __name__ == '__main__':` block.
    5.  Observe the `print` statements in the console for device state changes.
- **Code Snippet (Callbacks):**
    ```python
    def power_state(device_id, state):
        print('device_id: {} state: {}'.format(device_id, state))
        # Implement actual control of your light hardware here
        return True, state

    def set_brightness(device_id, brightness):
        print('device_id: {} brightness: {}'.format(device_id, brightness))
        # Implement actual control to set brightness
        return True, brightness

    def adjust_brightness(device_id, brightness):
        print('device_id: {} adjusted brightness level: {}'.format(device_id, brightness))
        # Implement actual control to adjust brightness
        return True, brightness

    def set_color(device_id, r, g, b):
        print('device_id: {} R:{},G:{},B:{}'.format(device_id, r, g, b))
        # Implement actual control to set color
        return True

    def set_color_temperature(device_id, color_temperature):
        print('device_id: {} color temperature:{}'.format(device_id, color_temperature))
        # Implement actual control to set color temperature
        return True

    def increase_color_temperature(device_id, value):
        print('device_id: {} value:{}'.format(device_id, value))
        # Implement actual control to increase color temperature
        return True, value

    def decrease_color_temperature(device_id, value):
        print('device_id: {} value:{}'.format(device_id, value))
        # Implement actual control to decrease color temperature
        return True, value
    ```
