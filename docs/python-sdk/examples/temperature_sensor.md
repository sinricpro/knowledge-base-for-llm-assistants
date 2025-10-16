## Temperature Sensor Example
- **File:** `temperature_sensor.py`
- **Description:** This example demonstrates how to integrate a temperature and humidity sensor with SinricPro. It focuses on sending `CURRENT_TEMPERATURE` events to the SinricPro platform, which can include both temperature and humidity data.
- **Key Features:**
    - `SinricProConstants.CURRENT_TEMPERATURE`: An event that can be raised to report current temperature and humidity readings.
- **Usage:**
    1.  Replace `APP_KEY`, `APP_SECRET`, and `TEMPERATURE_SENSOR_ID` with your actual SinricPro credentials and device ID.
    2.  Run the script: `python temperature_sensor.py`
    3.  **Complete Example (`temperature_sensor.py`):**
        ```python
        from sinric import SinricPro, SinricProConstants
        import asyncio
        from asyncio import sleep

        APP_KEY = ''
        APP_SECRET = ''
        TEMPERATURE_SENSOR_ID = ''


        async def events():
            while True:
                # client.event_handler.raise_event(TEMPERATURE_SENSOR_ID,\r\n        #                                  SinricProConstants.CURRENT_TEMPERATURE,\r\n        #                                  data= { SinricProConstants.HUMIDITY: 75.3, SinricProConstants.TEMPERATURE: 24})\r\n        # client.event_handler.raise_event(TEMPERATURE_SENSOR_ID, SinricProConstants.SET_POWER_STATE, data= {SinricProConstants.STATE: SinricProConstants.POWER_STATE_ON})
        # client.event_handler.raise_event(TEMPERATURE_SENSOR_ID, SinricProConstants.SET_POWER_STATE, data= {SinricProConstants.STATE: SinricProConstants.POWER_STATE_OFF})
        # Server will trottle / block IPs sending events too often.
                await sleep(60)

        callbacks = {}

        if __name__ == '__main__':
            loop = asyncio.get_event_loop()
            client = SinricPro(APP_KEY, [TEMPERATURE_SENSOR_ID], callbacks, event_callbacks=events,
                               enable_log=True, restore_states=False, secret_key=APP_SECRET)
            loop.run_until_complete(client.connect())

            # Example of simulating events (uncomment to use):
            # client.event_handler.raise_event(TEMPERATURE_SENSOR_ID, SinricProConstants.CURRENT_TEMPERATURE, data={'humidity': 75.3, 'temperature': 24})
        ```
    4.  **Sending Sensor Readings:**
        *   This example is designed to *send* events to SinricPro. The `events()` async function (commented out in the original example) shows how to periodically raise `CURRENT_TEMPERATURE` events with temperature and humidity data. Uncomment the relevant lines in the `events()` function to enable this.
        *   When these events are raised, SinricPro will update the sensor readings in the app and for connected voice assistants.
    5.  Observe the `print` statements in the console for confirmation that events are being sent.
- **Code Snippet (Event Raising):**
    ```python
    # Example of how to raise a CURRENT_TEMPERATURE event
    client.event_handler.raise_event(TEMPERATURE_SENSOR_ID,
                                     SinricProConstants.CURRENT_TEMPERATURE,
                                     data= { SinricProConstants.HUMIDITY: 75.3, SinricProConstants.TEMPERATURE: 24})
    ```
