---
title: SinricPro Modules
layout: post
---

### Introduction 

A module in the context of SinricPro is a self-contained physical unit, such as an ESP32 development board. These modules are designed to handle various tasks, primarily focusing on connectivity and communication, like establishing and maintaining WiFi connections. **A single module can host one or more virtual SinricPro devices**.

![what is a module](https://help.sinric.pro/public/img/sinricpro-what-is-a-module.png) 

This means that a single physical board can manage and control multiple smart home devices that appear individually within the SinricPro ecosystem and connected platforms like Amazon Alexa or Google Home. For example, an ESP32 module could be used to control several relays, each representing a distinct "switch" device in SinricPro.

You cannot create a module like virtual device. Once you connect a development board, they will appear in the Portal.

SinricPro provides following module features

 1. [Set Primary or Secondary WiFi](https://github.com/sinricpro/esp8266-esp32-sdk/tree/master/examples/Settings)
 
 2. [Check Health WiFi](https://github.com/sinricpro/esp8266-esp32-sdk/tree/master/examples/Health)

 3. [Set Fixed IP](https://github.com/sinricpro/esp8266-esp32-sdk/tree/master/examples/Settings)

 4. [OTA](https://help.sinric.pro/pages/ota)
 
