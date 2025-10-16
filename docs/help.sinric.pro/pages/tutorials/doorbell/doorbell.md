---
title: Smart Doorbell using  ESP32, ESP8266 or Raspberry Pi Pico W for Alexa, Google Home
layout: post
youtubeId: H-iftzWVTXE
youtubeId2: HEMifE1Xm7E
---

In this section we’ll walk through creating a **Doorbell** using a push (tactile) button connected to a **ESP32**, **ESP8266** or **Raspberry Pi Pico W** and then get a notification in Alexa, Google Home, or the Sinric Pro app.

### Prerequisites : 
1. ESP32, ESP8266 or Raspberry Pi Pico W x 1.
2. Push button x 1.
3. 1K ~ 10K resistor x 1
3. Jumper Wires.

### Wiring Doorbell button
We are going to use Pull-Down approach to wire our button. When the button is pressed, digitalRead reads HIGH (1) signal.

![Sinric Pro contact sensor wiring](https://help.sinric.pro/public/img/sinricpro_pi_doorbell-wiring-diagram.png) 

| MCU       | GPIO Pin     |
| --------- | ------- |
| ESP32     |    34   |
| ESP8266   |    4 (D2)  |
| Pico W    |    7    |

### Reading button state
Let's verify that sensor is wired correctly and working. 

```cpp
#if defined(ESP8266)
  #define BUTTON_PIN        D4
#elif defined(ESP32) 
  #define BUTTON_PIN        34
#elif (ARDUINO_ARCH_RP2040)
  #define BUTTON_PIN        7
#endif

int button_state;
int lastbutton_state = LOW;

unsigned long last_debounce_time = 0;
unsigned long debounce_delay = 50; // increase if needed.

void setup() {
  Serial.begin(115200);	
  pinMode(BUTTON_PIN, INPUT);
}

void handle_button_press() {
  int reading = digitalRead(BUTTON_PIN);  
  if (reading != lastbutton_state) {
    last_debounce_time = millis();
  }

  if ((millis() - last_debounce_time) > debounce_delay) {
    if (reading != button_state) {
      button_state = reading;
      
      if (button_state == HIGH) {
        Serial.printf("Pressed!\r\n");
      }
    }
  }
  
  lastbutton_state = reading;
}

void loop() {
  handle_button_press();
}

```

![Sinric Pro pull-down arduino serial monitor](https://help.sinric.pro/public/img/sinricpro-pushbutton-pull-down.png)

### Step 1 : Connect to Sinric Pro 
#### Create a new device: doorbell
* [Login](http://portal.sinric.pro) to your Sinric Pro account and go to **Devices** menu on your left.

* Click **Add Device** button (On top left).

* Enter the device name **doorbell**, description **smart doorbell** and select the type as **Doorbell**.

* Click **Save** to create the device

![Sinric Pro create device alexa](https://help.sinric.pro/public/img/sinricpro-doorbell-create-device.png)

Once you click on the save button Amazon Alexa will automatically detect the device we just created (If you have linked our Alexa skill before).

![Sinric Pro alexa doorbell notification](https://help.sinric.pro/public/img/sinricpro_alexa_doorbell_push_notification.jpg)

If you did not get the push notification, just ask Alexa to device devices

#### Step 1.1 Install Sinric Pro Library
![Sinric Pro install SinricPro library](https://help.sinric.pro/public/img/sinricpro_arduinoIDE-library-manager.png)

#### 2.2 Complete Code

```cpp
// Uncomment the following line to enable serial debug output
//#define ENABLE_DEBUG

#ifdef ENABLE_DEBUG
  #define DEBUG_ESP_PORT Serial
  #define NODEBUG_WEBSOCKETS
  #define NDEBUG
#endif 

#include <Arduino.h>
#if defined(ESP8266)
  #include <ESP8266WiFi.h>
#elif defined(ESP32) || defined(ARDUINO_ARCH_RP2040)
  #include <WiFi.h>
#endif

#include "SinricPro.h"
#include "SinricProDoorbell.h"

// Grab it from https://portal.sinric.pro.
#define APP_KEY    "" //Should look like "de0bxxxx-1x3x-4x3x-ax2x-5dabxxxxxxxx"
#define APP_SECRET "" //Should look like "5f36xxxx-x3x7-4x3x-xexe-e86724a9xxxx-4c4axxxx-3x3x-x5xe-x9x3-333d65xxxxxx"
#define DOORBELL_ID  "" //Should look like "6534xxxxxxxc08c6ea1b"

#define SSID       "" // Your WiFi SSID
#define PASS       "" // Your WiFi Password 

#define BAUD_RATE         115200                // Change baudrate to your need

#if defined(ESP8266)
  #define BUTTON_PIN        D4
#elif defined(ESP32) 
  #define BUTTON_PIN        34
#elif (ARDUINO_ARCH_RP2040)
  #define BUTTON_PIN        7
#endif

int button_state;
int lastbutton_state = LOW;

unsigned long last_debounce_time = 0;
unsigned long debounce_delay = 50; // increase if needed.
SinricProDoorbell& myDoorbell = SinricPro[DOORBELL_ID];

void handleButtonPress() {
  int reading = digitalRead(BUTTON_PIN);  
  if (reading != lastbutton_state) {
    last_debounce_time = millis();
  }

  if ((millis() - last_debounce_time) > debounce_delay) {
    if (reading != button_state) {
      button_state = reading;
      
      if (button_state == HIGH) {
        Serial.printf("[handleButtonPress()]: Button pressed!\r\n");
        bool success = myDoorbell.sendDoorbellEvent();
        if(!success) {
          Serial.printf("[handleButtonPress()]: Something went wrong...could not send Event to server!\r\n");
        }        
      }
    }
  }
  
  lastbutton_state = reading;
}
 
void setupWiFi() {
  Serial.printf("\r\n[setupWiFi()]: Connecting");

  #if defined(ESP8266)
    WiFi.setSleepMode(WIFI_NONE_SLEEP); 
    WiFi.setAutoReconnect(true);
  #elif defined(ESP32)
    WiFi.setSleep(false); 
    WiFi.setAutoReconnect(true);
  #endif

  WiFi.begin(WIFI_SSID, WIFI_PASS);

  while (WiFi.status() != WL_CONNECTED) {
    Serial.printf(".");
    delay(250);
  }
  IPAddress localIP = WiFi.localIP();
  Serial.printf("connected!\r\n[setupWiFi()]: IP-Address is %d.%d.%d.%d\r\n", localIP[0], localIP[1], localIP[2], localIP[3]);
}
 
void setupSinricPro() {
  SinricPro.onConnected([](){ Serial.printf("Connected to SinricPro\r\n"); }); 
  SinricPro.onDisconnected([](){ Serial.printf("Disconnected from SinricPro\r\n"); });
  SinricPro.begin(APP_KEY, APP_SECRET);
}

void setupButton() {
  pinMode(BUTTON_PIN, INPUT);
}

void setup() {
  Serial.begin(BAUD_RATE); Serial.printf("\r\n\r\n");
  setupButton();
  setupWiFi();
  setupSinricPro();
}

void loop() {
  handleButtonPress();
  SinricPro.handle();
}

```

**Note:** **Alexa doorbell notifications are turned off by default**. You must enable it by openning the app to recevice the DingDong notification in Alexa.

![Sinric Pro Alexa enable doorbell notification settings](https://help.sinric.pro/public/img/sinricpro-alexa-doorbell-settings.jpg)

{% include youtubePlayer.html id=page.youtubeId %}

{% include youtubePlayer.html id=page.youtubeId2 %}

### Troubleshooting
- Push button is too sensitive? You can adjust `debounce_delay` or try the perprosed solution in [issues/346](https://github.com/sinricpro/esp8266-esp32-sdk/issues/346)

- Please refer to our [Troubleshooting](https://help.sinric.pro/pages/troubleshooting.html) page for possible solutions to your issue.

> This document is open source. See a typo? Please create an [issue](https://github.com/sinricpro/help-docs)
