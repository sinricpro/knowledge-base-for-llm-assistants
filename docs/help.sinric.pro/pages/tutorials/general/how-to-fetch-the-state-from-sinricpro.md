
# How to fetch the device state from SinricPro

SinricPro is an event-driven IoT platform — meaning it pushes state changes (like ON/OFF) to your device via callbacks when users interact with devices through Alexa, Google Home, or the SinricPro dashboard.

However, you may need to **fetch or restore the current switch state** — for example, during device boot or after a reboot to sync with the cloud.

---

## Fetch Device State via REST API

You can fetch a device’s current state using SinricPro’s API via `curl`. This requires an API Key from SinricPro Portal -> Credentials

---

### Step 1: Authenticate and Get Access Token

```bash
curl -X POST 'https://api.sinric.pro/api/v1/auth' \
  --header 'x-sinric-api-key: a614xxxx-xxxx-xxxx-xxxx-xxxxxxxx'
```

> Replace `a614xxxx-xxxx-xxxx-xxxx-xxxxxxxx` with your actual **App Key** from the SinricPro portal.

#### Example Response:

```json
{
  "success": true,
  "message": "OK.",
  "accessToken": "eyJhbG.xxxxxxxxx.xxxxxxxxxxxxxxx...",
  "refreshToken": "9i4GV2Llpsl87FoT1HvQcxaybP3xxxxxxxxxxxxxxxx..",
  "expiresIn": 604800
}
```

---

### Step 2: Fetch Device State

Use the `accessToken` to get the device’s current state:

```bash
curl -X GET 'https://api.sinric.pro/api/v1/devices/{device_id}' \
  --header 'Authorization: Bearer eyJhbG.xxxxxxxxx.xxxxxxxxxxxxxxx...'
```

> Replace:
> - `{device_id}` with your actual device ID (e.g., `60a8b1c23d9f7f000123abcd`)
> - `eyJhbG...` with your actual `accessToken`

#### ✅ Example Response:

```json
{
  "deviceId": "60a8b1c23d9f7f000123abcd",
  "deviceType": "SWITCH",
  "name": "Living Room Light",
  "description": "",
}
```

### Alternative: Store State Locally
Save the latest state to EEPROM, SPIFFS, or Flash inside the `onPowerState` callback:

```cpp
void saveStateToFlash(bool state) {
  // Example: Save to EEPROM or Preferences (ESP32)
  // Implement your preferred non-volatile storage here
}
```
---

