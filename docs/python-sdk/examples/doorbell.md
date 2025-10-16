## Doorbell Example
- **File:** `doorbell.py`
- **Description:** This example demonstrates how to integrate a doorbell with SinricPro, specifically focusing on sending `DoorbellPress` events to the SinricPro platform. This allows for notifications and automations when the doorbell is pressed.
- **Key Features:**
    - `SinricProConstants.DOORBELLPRESS`: An event that can be raised to indicate a doorbell press.
- **Usage:**
    1.  Replace `APP_KEY`, `APP_SECRET`, and `DOORBELL_ID` with your actual SinricPro credentials and device ID.
    2.  **Important:** Ensure "Doorbell Press" is enabled in your Alexa app settings for the doorbell device.
    3.  Run the script: `python doorbell.py`
    4.  **Complete Example (`doorbell.py`):**
        ```python
        from sinric import SinricPro, SinricProConstants
        import asyncio
        from asyncio import sleep

        APP_KEY = ''
        APP_SECRET = ''
        DOORBELL_ID = ''

        '''
        DON'T FORGET TO TURN ON 'Doorbell Press' IN ALEXA APP
        '''


        async def events():
            while True:
                # client.event_handler.raise_event(DOORBELL_ID, SinricProConstants.DOORBELLPRESS)
                # await sleep(60) # Server will trottle / block IPs sending events too often.
                pass

        callbacks = {}

        if __name__ == '__main__':
            loop = asyncio.get_event_loop()
            client = SinricPro(APP_KEY, [DOORBELL_ID], callbacks, event_callbacks=events,
                               enable_log=True, restore_states=False, secret_key=APP_SECRET)
            loop.run_until_complete(client.connect())

            # Example of simulating events (uncomment to use):
            # client.event_handler.raise_event(DOORBELL_ID, SinricProConstants.DOORBELLPRESS)
        ```
    5.  **Triggering a Doorbell Press:**
        *   This example is designed to *send* events to SinricPro. To simulate a doorbell press, uncomment the `client.event_handler.raise_event` line within the `events()` async function or call it directly when a physical doorbell press occurs.
        *   When this event is raised, SinricPro will notify connected voice assistants (like Alexa) and the SinricPro app, triggering any configured routines or notifications.
- **Code Snippet (Event Raising):**
    ```python
    # Example of how to raise a DoorbellPress event
    client.event_handler.raise_event(DOORBELL_ID, SinricProConstants.DOORBELLPRESS)
    ```
