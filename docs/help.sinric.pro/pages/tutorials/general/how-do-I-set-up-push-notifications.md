# Setting Up Push Notifications for Devices in SinricPro

SinricPro supports built-in push notifications that can be triggered by device events or sent manually via your device firmware. Follow the steps below to configure and send push notifications.

---

## 1. Built-in: Enable Notifications via SinricPro Portal

### Device Creation & Configuration

1. Go to the [SinricPro Portal](https://portal.sinric.pro) and either:
   - Create a new device, or
   - Select an existing device from your dashboard.

2. Navigate to the **Notifications** tab (available during or after device creation).

3. Under **Alerts**, toggle on **Enable Notifications**.

4. Select the specific **events** that should trigger a push notification:
   - Power On / Off
   - Motion Detected
   - Door Opened / Closed
   - Custom Capabilities (if applicable)
   - Ect..

5. Set the **Retrigger Time** (in seconds):
   - Controls how frequently the same notification can be resent.
   - Prevents spam — e.g., setting to `300` means the same alert won’t repeat within 5 minutes.

> Notifications will appear in the **SinricPro mobile app** (iOS/Android) for users logged into the same account.

---

## 2. Send Custom Push Notifications from Device Firmware

You can also trigger custom push notifications programmatically from your microcontroller or server.

### For ESP8266 / ESP32 / Arduino (C++)

```cpp
#include <SinricPro.h>

// Assuming you have a switch device
SinricProSwitch& mySwitch = SinricPro[SWITCH_ID];

// Send a custom push notification
mySwitch.sendPushNotification("Hello from my ESP32!");
```

> Replace `SWITCH_ID` with your actual device ID (e.g., `"5eb4abc123456789abcdef01"`).

---

### ➤ For Python (Using SinricPro SDK)

```python
from sinric import SinricPro

# Assuming you have initialized the client
client.event_handler.raise_event(
    device_id=DEVICE_ID,
    event_name=SinricProConstants.PUSH_NOTIFICATION,
    data={'alert': "Your custom message here!"}
)
```

> Replace `DEVICE_ID` with your device’s ID string.

---

## Key Considerations

- **Retrigger Time**: Notifications are rate-limited based on the value set in the portal. Adjust this to suit your use case (e.g., security alerts vs. informational messages).

- **Authentication**: Ensure your device is authenticated with valid `APP_KEY` and `APP_SECRET`. Notifications will fail if authentication is invalid or expired.

- **Mobile App Required**: End users must have the **SinricPro mobile app** installed and be logged into the same account to receive push alerts.

- **Event-Based vs Custom**: Built-in event notifications are automatic (e.g., “Light turned ON”). Custom notifications let you send any message (e.g., “Temperature threshold exceeded!”).

---

## Example Use Cases

- Notify when a door sensor is triggered after midnight.
- Alert when a washing machine cycle completes.
- Send a custom status update: “Garage door left open for 10 minutes!”

--- 