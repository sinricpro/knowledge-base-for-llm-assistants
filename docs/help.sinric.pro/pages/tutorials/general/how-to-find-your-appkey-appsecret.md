# How to Find Your App Key (AppKey) and (AppSecret) in Sinric Pro

In **Sinric Pro**, your **App Key** and **App Secret** are required to authenticate your IoT devices (e.g., ESP8266, ESP32) with the Sinric Pro cloud server.

---

## Steps to Locate App Key & App Secret

1. **Log in** to your Sinric Pro account: [https://portal.sinric.pro](https://portal.sinric.pro)

2. Navigate to the **Dashboard**.

3. In the left sidebar, click **Credentials**.

4. Under the **Your App Keys and Secrets** section, you’ll find:
   - `App Key` (Application Key)
   - `App Secret`

   > 🔐 *The App Secret is hidden by default.* Click the **👁️ Show** button to reveal it.

5. **Copy both values** — you’ll need them in your device firmware or app code.

---

## Example Usage (Arduino/ESP)

```cpp
#define APP_KEY         "your-app-key-here"
#define APP_SECRET      "your-app-secret-here"