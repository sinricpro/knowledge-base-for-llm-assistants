### Sending Voice Announcements via Google Home Using SinricPro

Google Home **does not support arbitrary text-to-speech (TTS) announcements** through the Smart Home API for third-party integrations like SinricPro. However, you can achieve voice announcements **indirectly** using **Google Home Routines** (called *“Routines”* in the Google Home app) combined with a virtual device trigger.

---

## Method: Google Home Routines + SinricPro Switch

### Step-by-Step:

1. **Create a Google Home Routine**  
   - Open the **Google Home app**.  
   - Go to **Routines** → **+ Create new routine**.  
   - Under **“When this happens”**, choose **“Device state”** (e.g., “When [Device] turns on”).  
   - Under **“Add action”**, select **“Assistant voice”** or **“Speak response”**, and enter your custom announcement (e.g., “The garage door is open.”).  
   - Save the routine.

2. **Add a Switch in SinricPro**  
   - In your **SinricPro dashboard**, create a new **Switch** device.  
   - Give it a recognizable name (e.g., “Announcement Trigger”).

3. **Trigger the Announcement**  
   - From your code or automation system, send a `sendPowerStateEvent` command to turn **on** the Switch via SinricPro.  
   - This state change is detected by Google Home and triggers the associated routine, causing the Google Assistant to speak your message.

---

Example code for ESP8266/ESP32:

```
void triggerVoiceAnnouncement() {
    SinricProSwitch& mySwitch = SinricPro[SWITCH_ID];
    mySwitch.sendPowerStateEvent(true);
} 
```
---

### Limitation:
This approach supports **predefined announcements only**. If you need to send **dynamic/runtime messages** (e.g., “Current temperature is 23°C”), Google Home **does not support this** via standard Smart Home APIs. In such cases, you’d need to explore alternative solutions like **broadcast notifications on Android devices** or **third-party TTS speakers**, which fall outside the official Google Smart Home framework.

---
