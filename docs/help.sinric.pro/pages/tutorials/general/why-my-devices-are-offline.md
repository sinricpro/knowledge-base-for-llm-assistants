If your devices are showing as **“offline” in SinricPro**, it’s usually due to a communication breakdown between your physical device (e.g., ESP8266/ESP32) and the SinricPro cloud server. Here’s a structured troubleshooting guide:

---

## Quick Fix Checklist

| Step | Action |
|------|--------|
| ☑️ | Reset the power to the device |
| ☑️ | Re-check APP_KEY, SECRET, DEVICE_ID |
| ☑️ | Missing `SinricPro.handle()` in `loop()` ? |
| ☑️ | Long `delay(5000)` in `loop()` ? |
| ☑️ | Enable debug logs `#define ENABLE_DEBUG` |
| ☑️ | Watch Serial Monitor for connection messages |
| ☑️ | Test with example sketch |

---

## 1. Unstable Connection?

If your ESP32/ESP8266 keeps toggling between online/offline on SinricPro, **avoid `delay()` or long blocking code** — it breaks Wi-Fi stability. Short delays (<100ms) are usually safe; longer ones cause disconnects.

**Fix:** Replace `delay()` with non-blocking timing using `millis()`.

**Bad:**
```cpp
void loop() {
  delay(5000); // ❌ Never do this!
}
```

**Good:**
```cpp
const int ledPin = 2;
unsigned long prevMillis = 0;
const long interval = 1000;

void setup() { pinMode(ledPin, OUTPUT); }

void loop() {
  if (millis() - prevMillis >= interval) {
    prevMillis = millis();
    digitalWrite(ledPin, !digitalRead(ledPin));
  }
}
```

---

**2. Auto-Reconnect on WiFi Drop**

In `loop()`, add:
```cpp
if (WiFi.status() != WL_CONNECTED) {
  delay(1000);
  ESP.restart(); // or WiFi.reconnect();
}
```

And always call:

```cpp
void loop() {
  SinricPro.handle(); // Required in loop
}
```

 
---

## 2. **Check Physical Device Status**
- Is your microcontroller (ESP8266/ESP32, etc.) powered on?
- Is it connected to Wi-Fi? Check serial monitor logs for:
  ```
  Connected to WiFi
  IP Address: 192.168.x.x
  ```
- If not connecting to Wi-Fi → fix SSID/password or signal strength.

---

## 3. **Check Internet Connectivity**
Your device needs internet access to reach SinricPro servers.
- Can it ping external sites? (e.g., via `WiFiClient` test)
- Are you behind a firewall, proxy, or captive portal (like in hotels/schools)? These can block WebSocket connections.

---

## 4. **Verify SinricPro Credentials**
In your device code, ensure you’re using the correct:
- `APP_KEY`
- `APP_SECRET`
- `DEVICE_ID`

> These are **case-sensitive** and must match exactly what’s shown in your [SinricPro Dashboard](https://portal.sinric.pro).

💡 Tip: Re-copy them directly from the portal — don’t type manually.

---

## 5. **WebSocket Connection Failure**
SinricPro uses WebSockets. Look in Serial Monitor for:
```
Connected to SinricPro
```
or errors like:
```
[Websocket] connection failed
Disconnected from SinricPro
```
Common causes:

- Wrong firmware version (update SinricPro library)
- Server outage (check https://sinric.pro)
- Network blocks port 80/443 or WebSocket traffic

---

## 6. **Library & Firmware Version**
Ensure you’re using the **latest SinricPro SDK**

Update via Library Manager or GitHub:
🔗 https://github.com/sinricpro/esp8266-esp32-sdk

---


## 7. **Device ID does not exsists**
Log into [SinricPro Portal](https://portal.sinric.pro):

- Go to **Devices** → Is your device listed?
- Is its status “Offline”? Hover to see last-seen timestamp.
- Was it accidentally deleted or unclaimed?

→ If missing, re-add it using the same `DEVICE_ID`.

---

## 8. **Rate Limits or Banned IP?**
SinricPro may temporarily throttle or block IPs that:
- Send too many rapid requests
- Have invalid auth attempts

💡 Wait 60 minutes and retry. Use static IP or check if your ISP changed your public IP.

---

## 9. **Enable Debug Logs**
Add this to top of the sketch:

```cpp
#define DEBUG_ESP_PORT Serial
#define ENABLE_DEBUG

.....

SinricPro.onConnected([](){ Serial.println("Connected to SinricPro"); });
SinricPro.onDisconnected([](){ Serial.println("Disconnected from SinricPro"); });
```

Watch Serial Monitor for detailed connection flow.

---

## 10. **Test with Example Code**
Try running one of the official examples (e.g., Switch or LightBulb) with your credentials — if it works, the issue is in your custom code.

---

## Still Not Working?

### Do this:
1. Copy-paste your **Serial Monitor output** (from boot to failure).
2. Confirm SDK version.
3. Share whether other devices work (to isolate hardware vs account issue).

---



✅ Once fixed, your device should show **“Online”** in the SinricPro dashboard within 10–30 seconds of successful connection.

---
