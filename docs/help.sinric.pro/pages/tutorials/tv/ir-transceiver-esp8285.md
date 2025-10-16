---
title: TV Tutorial - IR Transceiver
layout: post
---

In this section we’ll walk through controlling your TV using an IR transceiver and then use voice to control the TV via **Alexa, Google Home or SmartThings**.

### Prerequisites : 
1. ESP8285 ESP-01M IR Transceiver x1. 

In this tutorial, we'll be using the ESP8285 ESP-01M IR Transceiver, a popular option readily available [online](https://www.google.com/search?q=esp8285+esp-01m+ir+transceiver). While we'll be focusing on this specific module, the concepts can be applied to other 38KHz infrared transmitters as well.

![Sinric Pro ir transceiver](https://help.sinric.pro/public/img/sinricpro-ir-transceiver-esp8285.png) 

### Quick introduction to ESP8285 ESP-01M IR Transceiver
An Infrared ESP8285 Wireless WIFI Transceiver Module is a circuit board that combines two functionalities:

**Infrared (IR) Transceiver:** This part allows the module to transmit and receive infrared signals. IR transmitter is connected to `GPIO4` and IR receiver is connected to `GPIO14`

**ESP8285 Wi-Fi Module:** This is a separate chip on the board that enables the module to connect to a Wi-Fi network. ESP8285 is a ESP8266 with 1M Flash, which can withstand high temperatures up to 125 degrees Celsius.

**To Upload Firmware** : Connect the Jumper `GPIO01` and `GND`.

**To Run Firmware** : Remove the Jumper and replug the USB programmer

### Wiring
![Sinric Pro esp8266 relay wiring](https://help.sinric.pro/public/img/sinricpro-ir-transceiver-esp8285-wiring.png) 

| ESP8285   | TTL     |
| --------- | ------- |
| 5V        |    5V   |
| GND       |    GND  |
| TX        |    RX   |
| RX        |    TX   |

Before we integrate with Sinric Pro, it is important to verify that the relay is wired correctly and you can record IR signals. 

### Step 1: Recording IR
1. Install [IRremoteESP8266](https://github.com/crankyoldgit/IRremoteESP8266) 

    * Click the "Sketch" -> "Include Library" -> "Manage Libraries..." Menu items.

    * Enter `IRremoteESP8266` into the "Filter your search..." top right search box.

    * Click on the `IRremoteESP8266` result of the search.

    * Select the version you wish to install and click `Install`.

2. Connect the Jumper `GPIO0` to `GND` to upload the firmware.

3. Flash the [IRrecvDumpV3](https://github.com/crankyoldgit/IRremoteESP8266/blob/master/examples/IRrecvDumpV3/IRrecvDumpV3.ino) No need to change the sketch. It's already using correct GPIO pins.

![transceiver esp8285 flash](https://help.sinric.pro/public/img/sinricpro-ir-transceiver-esp8285-flash.png) 

4. Remove the Jumper connecting `GPIO0` to `GND`  and replug the USB programmer. Now ESP chip will run the `IRrecvDumpV3` sketch. Set the Arduino Serial BaudRate to `115200` see the logs.
  
6. Time to record the **power on** button of your TV remote control.

![record tv remote esp82266](https://help.sinric.pro/public/img/sinricpro-ir-transceiver-record-tv-remote.png) 

Would output the IR codes like this below in the Arduino Serial Monitor. 

Copy **rawData** array

![record tv remote esp82266 arduino serial](https://help.sinric.pro/public/img/sinricpro-ir-transceiver-record-tv-remote-serial-arduino.png) 

### Step 2: Sending IR

```c++
#include <Arduino.h>
#include <IRremoteESP8266.h>
#include <IRsend.h>

const uint16_t kIrLed = 4;

IRsend irsend(kIrLed);

uint16_t rawData[3] = {9004, 2218,  586};  // TODO: Change this to your remote control power button

void setup() {
  irsend.begin();
  Serial.begin(115200);
}

void loop() {
  Serial.println("a rawData capture from dump");
  irsend.sendRaw(rawData, 3 /* IR len */, 38 /* kHz */);  // TODO: adjust IR len according to rawData length.
  delay(2000);
}

```

**Flash the above sketch and remove the the Jumper to run the the firmware.**

When the sketch sends the signal, TV should either turn on or off. Now you can repeate the process to record other buttons such as volume up, down, mute ect..

### Step 3 : Create a new TV device in Sinric Pro
* [Login](http://portal.sinric.pro) to your Sinric Pro account, go to **Devices** menu on your left and click **Add Device** button (On top left).

* Enter the device name **TV**, description **TV** and select the device type as **TV**.

* Click Others and Click **Save**

![record tv remote esp82266 arduino serial](https://help.sinric.pro/public/img/sinricpro-tv-new-device.png) 

![Sinric Pro copy device id](https://help.sinric.pro/public/img/sinric-pro-create-device-keys-no-hilight.png)

* Copy the **Device Id**, **App Key** and **App Secret** ***Keep these values secure. DO NOT SHARE THEM ON PUBLIC FORUMS !***

### Step 2 : Coding 
#### 2.1 Install Sinric Pro Library
![Sinric Pro install SinricPro library](https://help.sinric.pro/public/img/sinricpro_arduinoIDE-library-manager.png)

#### 2.2 Complete Code
Now we can complete the SinricPro TV sketch with the IR codes.

```cpp
// #define ENABLE_DEBUG
#define SINRICPRO_NOSSL
 
#ifdef ENABLE_DEBUG
  #define DEBUG_ESP_PORT Serial
  #define NODEBUG_WEBSOCKETS
  #define NDEBUG
#endif 

#include <Arduino.h>
#include <ESP8266WiFi.h>
#include <IRremoteESP8266.h>
#include <IRsend.h>

#include "SinricPro.h"
#include "SinricProTV.h"

#define WIFI_SSID         "YOUR-WIFI-SSID"    
#define WIFI_PASS         "YOUR-WIFI-PASSWORD"
#define APP_KEY           "YOUR-APP-KEY"      // Should look like "de0bxxxx-1x3x-4x3x-ax2x-5dabxxxxxxxx"
#define APP_SECRET        "YOUR-APP-SECRET"   // Should look like "5f36xxxx-x3x7-4x3x-xexe-e86724a9xxxx-4c4axxxx-3x3x-x5xe-x9x3-333d65xxxxxx"
#define TV_ID             "YOUR-DEVICE-ID"    // Should look like "5dc1564130xxxxxxxxxxxxxx"
#define BAUD_RATE         115200

const uint16_t kIrLed = 4;
IRsend irsend(kIrLed);

bool tvPowerState;

bool onPowerState(const String &deviceId, bool &state) {
  Serial.printf("TV turned %s\r\n", state?"on":"off");
  tvPowerState = state; // set powerState

  /*
    Protocol  : NEC
    Code      : 0x20DF10EF (32 Bits)
    uint16_t rawData[71] = {9032, 4476,  582, 544,  582, 546,  582, 1652,  582, 546,  582, 544,  582, 546,  582, 546,  582, 546,  582, 1654,  582, 1654,  582, 544,  582, 1654,  582, 1654,  582, 1654,  582, 1654,  582, 1654,  582, 546,  582, 544,  584, 544,  582, 1654,  582, 546,  582, 546,  582, 546,  582, 546,  582, 1654,  582, 1654,  582, 1652,  582, 546,  582, 1654,  582, 1654,  582, 1654,  582, 1654,  582, 41158,  9004, 2222,  582};  // NEC 20DF10EF
    uint32_t address = 0x4;
    uint32_t command = 0x8;
    uint64_t data = 0x20DF10EF;
  */
  
  uint16_t rawData[71] = {9032, 4476,  582, 544,  582, 546,  582, 1652,  582, 546,  582, 544,  582, 546,  582, 546,  582, 546,  582, 1654,  582, 1654,  582, 544,  582, 1654,  582, 1654,  582, 1654,  582, 1654,  582, 1654,  582, 546,  582, 544,  584, 544,  582, 1654,  582, 546,  582, 546,  582, 546,  582, 546,  582, 1654,  582, 1654,  582, 1652,  582, 546,  582, 1654,  582, 1654,  582, 1654,  582, 1654,  582, 41158,  9004, 2222,  582};  // NEC 20DF10EF
  irsend.sendRaw(rawData, 71 /* IR len */, 38 /* kHz */);
  
  return true; 
} 

// setup function for WiFi connection
void setupWiFi() {
  Serial.printf("\r\n[Wifi]: Connecting");

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
  Serial.printf("connected!\r\n[WiFi]: IP-Address is %d.%d.%d.%d\r\n", localIP[0], localIP[1], localIP[2], localIP[3]);
}

// setup function for SinricPro
void setupSinricPro() {
  // add device to SinricPro
  SinricProTV& myTV = SinricPro[TV_ID];

  // set callback functions to device
  myTV.onPowerState(onPowerState);

  // For other https://github.com/sinricpro/esp8266-esp32-sdk/blob/master/examples/TV/TV.ino
  // myTV.onAdjustVolume(onAdjustVolume);
  // myTV.onChangeChannel(onChangeChannel);
  // myTV.onChangeChannelNumber(onChangeChannelNumber);
  // myTV.onMediaControl(onMediaControl);
  // myTV.onMute(onMute);  
  // myTV.onSelectInput(onSelectInput);
  // myTV.onSetVolume(onSetVolume);
  // myTV.onSkipChannels(onSkipChannels);

  // setup SinricPro
  SinricPro.onConnected([](){ Serial.printf("Connected to SinricPro\r\n"); }); 
  SinricPro.onDisconnected([](){ Serial.printf("Disconnected from SinricPro\r\n"); });
  SinricPro.begin(APP_KEY, APP_SECRET);
}

void setupIR() {
  irsend.begin();
}

// main setup function
void setup() {
  setupIR();
  
  Serial.begin(BAUD_RATE); Serial.printf("\r\n\r\n");

  setupWiFi();
  setupSinricPro();
}

void loop() {
  SinricPro.handle();
}

```

[Video: https://help.sinric.pro/public/video/sinricpro-ir-tv-demo.mp4](https://help.sinric.pro/public/video/sinricpro-ir-tv-demo.mp4)

Now you should be able to control the TV via Sinric Pro App, Google Home, Alexa as well.

### Troubleshooting
Please refer to our [Troubleshooting](https://help.sinric.pro/pages/troubleshooting.html) page for possible solutions to your issue.

> This document is open source. See a typo? Please create an [issue](https://github.com/sinricpro/help-docs)
