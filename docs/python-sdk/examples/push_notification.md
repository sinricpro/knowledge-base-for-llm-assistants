## Push Notification Example
- **File:** `push_notification.py`
- **Description:** This example demonstrates how to send push notifications from your SinricPro device to the SinricPro app or integrated smart home assistant. It shows how to raise a `PUSH_NOTIFICATION` event with a custom alert message.
- **Key Features:**
    - `SinricProConstants.PUSH_NOTIFICATION`: An event that can be raised to send a push notification.
- **Usage:**
    1.  Replace `APP_KEY`, `APP_SECRET`, and `DEVICE_ID` with your actual SinricPro credentials and the device ID for which you want to send notifications.
    2.  Run the script: `python push_notification.py`
    3.  **Complete Example (`push_notification.py`):**
        ```python
        from sinric import SinricPro, SinricProConstants
        import asyncio
        from asyncio import sleep

        APP_KEY = ''
        APP_SECRET = ''
        DEVICE_ID = ''


        async def events():
            while True:
                client.event_handler.raise_event(
                    DEVICE_ID, SinricProConstants.PUSH_NOTIFICATION, data={'alert': "Hello there"})
                # Server will trottle / block IPs sending events too often.
                await sleep(60)

        callbacks = {}

        if __name__ == '__main__':
            loop = asyncio.get_event_loop()
            client = SinricPro(APP_KEY, [DEVICE_ID], callbacks, event_callbacks=events,
                               enable_log=True, restore_states=False, secret_key=APP_SECRET)
            loop.run_until_complete(client.connect())
        ```
    4.  **Sending Notifications:**
        *   This example is designed to *send* events to SinricPro. The `events()` async function continuously raises a `PUSH_NOTIFICATION` event.
        *   When this event is raised, SinricPro will send a push notification to the SinricPro mobile app (if configured and logged in) and potentially trigger routines in connected voice assistants.
    5.  Observe the console for confirmation that events are being sent.
- **Code Snippet (Event Raising):**
    ```python
    # Example of how to raise a PUSH_NOTIFICATION event
    client.event_handler.raise_event(
        DEVICE_ID, SinricProConstants.PUSH_NOTIFICATION, data={'alert': "Your custom message here!"})
    ```
