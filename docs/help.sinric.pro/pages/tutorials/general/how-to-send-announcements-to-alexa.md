# Sending Voice Announcements via Alexa Using SinricPro

The Amazon Alexa Smart Home API does not natively support sending arbitrary voice announcements (i.e., custom text-to-speech messages). However, you can achieve this functionality indirectly using only officially supported methods by combining **Alexa Routines** with a **Switch** in SinricPro.

## Method: Alexa Routines + SinricPro Switch

Follow these steps to enable Alexa voice announcements triggered from your SinricPro integration:

1. **Create an Alexa Routine**  
   In the Alexa app, set up a new Routine that speaks your desired message. Configure it to be triggered by a specific smart home event—such as “When a smart device turns on.”

2. **Add a Switch in SinricPro**  
   In your SinricPro dashboard, create a new **Switch** device (this will act as your virtual trigger).

3. **Trigger the Announcement**  
   From your application or server, send a SinricPro `sendPowerStateEvent` event to turn **on** the virtual switch. This action will activate the Alexa Routine, causing Alexa to speak your announcement.

---

Example code for ESP8266/ESP32:

```
void triggerVoiceAnnouncement() {
    SinricProSwitch& mySwitch = SinricPro[SWITCH_ID];
    mySwitch.sendPowerStateEvent(true);
} 
```
---

> **Note:** This approach leverages only Amazon-approved mechanisms and does not require any unofficial workarounds.