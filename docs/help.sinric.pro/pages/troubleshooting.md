---
title: Troubleshooting Tips
layout: post
---

### Troubleshooting tips 

### Connection is unstable (Device cycles between "online" and "offline" on SinricPro dashboard) 

In ESP32 / ESP8266, adding a delay() (or long blocking code) directly affects Wi-Fi performance and stability, because of how the Wi-Fi stack works. So short delays (few ms) are usually safe. But long delays (> ~100ms) can cause Wi-Fi timeouts, missed packets, or disconnects.

If you have any long delay(x); in the loop() function, you may need to rewrite  them to use them in a non-blocking manner.

Example (bad code):


```c++
void setup() {   
}

void loop() {
    delay(5000) // DO NOT DO THIS!
}
```

Example (good code):

```c++
const int ledPin = 2;

// Variables to manage timing
unsigned long previousMillis = 0;  // Stores last time LED was updated
const long interval = 1000;        // Interval for blinking (milliseconds)

void setup() {
    // Initialize the LED pin as an output
    pinMode(ledPin, OUTPUT);
}

void loop() {
    // Get the current time
    unsigned long currentMillis = millis();

    // Check if the interval has passed
    if (currentMillis - previousMillis >= interval) {
        // Save the current time
        previousMillis = currentMillis;

        // Toggle the LED state
        digitalWrite(ledPin, !digitalRead(ledPin));
    }
}
```

### Known Issue: WiFi Instability on ESP32/ESP8266 When Using ADC Pins

If you're developing with **ESP32** or **ESP8266** and noticing erratic WiFi behavior — such as frequent disconnects, poor signal strength, or failed connections — you might be running into a well-documented hardware quirk: **WiFi instability when analog-to-digital converter (ADC) pins are in use**.

#### What’s Happening?

Both the ESP32 and ESP8266 use shared internal resources for WiFi radio and ADC operations. When the ADC is actively sampling (especially at high frequency or with continuous reads), it can interfere with the RF circuitry responsible for WiFi communication. This results in:

- Dropped WiFi connections
- Increased latency or packet loss
- Difficulty maintaining association with access points
- In extreme cases, complete WiFi failure until reboot

This issue is particularly noticeable when:

- Reading analog sensors (e.g., potentiometers, LDRs, analog joysticks) in tight loops
- Using `analogRead()` frequently without delays
- Sampling at high resolution or rate on ESP32’s ADC

#### Workarounds & Best Practices

1. **Add Delays Between ADC Reads**  
   Avoid continuous polling. Introduce small delays (e.g., `delay(10)` or `vTaskDelay()`) between analog reads to give the WiFi stack breathing room.

   ```cpp
   int sensorValue = analogRead(A0);
   delay(10); // Let WiFi recover

## Cannot connect to SinricPro

* Try starting a hotspot from your mobile phone and then connect your ESP to SinricPro via the hotspot. If you can connect to SinricPro via the hotspot, then the problem is with your WiFi network. 

* Try switching to a different WiFi network if possible.    

* Always start by trying our example sketches, changing only the credentials. They have been thoroughly tested and are known to work correctly.

#### Logging 

##### SinricPro SDK Logging

* Enable logging and check for errors, and disconnections. To enable logs Sinric Pro SDK logs, add ``#define ENABLE_DEBUG`` to top of the sketch.

##### ESP8266/ESP32 Core Logging

    * To enable ESP8266 logs, in Arduinio IDE:

        1. `Tools -> Debug Serial Port -> Serial`

        2. `Tools -> Debug Level -> SSL + HTTP_CLIENT`

    * To enable ESP32 logs, in Arduinio IDE:

        1. `Tools -> Core Debug Level -> Verbose`

#### Memory Limitation 

* Sometimes, due to memory limitations, the ESP chip may fail while trying to establish the SSL connection to the SinricPro server. In this case, you can try disabling the SSL feature by adding `#define SINRICPRO_NOSSL` to the **top of the sketch (before #include "SinricPro.h")**:

    ```c++
    #define SINRICPRO_NOSSL
    #include "SinricPro.h"
    ...
    ```


 
* If none of above works for you, check for existing [issues](https://github.com/sinricpro/esp8266-esp32-sdk/issues) in GitHub repo or open a [new one](https://github.com/sinricpro/esp8266-esp32-sdk/issues/new)

* Join the SinricPro on Discord : https://discord.gg/CqjBrkM6
