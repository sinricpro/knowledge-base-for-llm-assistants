# How to Control a SinricPro Device Using MacroDroid on Android

To trigger or control a device added in **SinricPro** via the **MacroDroid** app on Android, you need to send an HTTP POST request to SinricPro’s API. MacroDroid does not have built-in SinricPro support, but its **HTTP Request** action makes this possible.

---

## 🔧 Prerequisites

- ✅ Login to [Sinric Portal](https://portal.sinric.pro), select **Credentials** and click on **New API Key** click **save** and **Copy** the newly created API Key
- ✅ The **MacroDroid** app installed on your Android device.
- ✅ Your SinricPro device (e.g., ESP32/ESP8266) must be online and connected.

---

## 📡 Step-by-Step Guide

### 1. Retrieve SinricPro Credentials

1. Log in to [https://portal.sinricpro.com](https://portal.sinricpro.com).
2. Go to **Devices** → Click your target device → Copy the **Device ID**.
3. Go to **Credentials** and click on **New API Key** click **Save** and **Copy** the newly created API Key

> 🔒 Never share your API Key—it acts as your authentication token.

---

### 2. Prepare the API Request

SinricPro accepts control commands via its asynchronous API endpoint:

- **URL**:  
  ```
  https://apple.sinric.pro/v1/shortcuts/actions
  ```

- **Headers**:
  ```http
  Content-Type: application/json
  ```

- **Method**:
  ```POST```

- **JSON Payload (Example: Turn ON)**:
  ```json
  { "api_key": "YOUR_API_KEY_FROM_ABOVE", "device_id": "YOUR_DEVICE_ID", "action": "setPowerState", "value": { "state" : "On"} }
  ```

- **To Turn OFF**, use:
  ```json
  { "api_key": "YOUR_API_KEY_FROM_ABOVE", "device_id": "YOUR_DEVICE_ID", "action": "setPowerState", "value": { "state" : "Off"} }
  ```

> 💡 This method works for devices that support the `setPowerState` action (most basic switches, lights, and smart plugs).

---

### 3. Create a Macro in MacroDroid

1. Open **MacroDroid**.
2. Tap the **+** button to create a new macro.
3. **Set a Trigger** (e.g., “Button Widget”, “Voice Command”, “Time/Date”, etc.).
4. Add an **Action**:
   - Tap **Actions** → **Network** → **HTTP Request**.
   - Configure the request:
     - **Method**: `POST`
     - **URL**: `https://apple.sinric.pro/v1/shortcuts/actions`
     - **Headers**:
       ```
       Content-Type: application/json
       ```
     - **Body (Raw)**: Paste your JSON payload (e.g., the ON or OFF command).
5. *(Optional)* Add a **Toast Message** or **Vibration** to confirm execution.
6. Tap **Save**.

---

### 4. Test the Macro

- Run the macro manually from MacroDroid or via your chosen trigger.
- Observe whether your SinricPro-connected device responds (e.g., relay toggles).
- If it fails:
  - Double-check your **Device ID** and **App Key**.
  - Ensure your device is online in the SinricPro portal.
  - Use a tool like **Postman** or **curl** to test the same request independently.

---

## ⚠️ Notes & Limitations

- The `/actions` endpoint is **asynchronous**—there may be a slight delay (1–3 seconds).
- Not all SinricPro device types support every action. Check the [SinricPro documentation](https://sinricpro.github.io/esp8266-esp32-sdk/) for supported actions.
- Avoid exposing your App Key in logs or public macros.

---

✅ With this setup, you can now control your SinricPro devices directly from MacroDroid using custom triggers like voice, location, time, or widgets!