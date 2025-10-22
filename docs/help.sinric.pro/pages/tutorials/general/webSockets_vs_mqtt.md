---
title: SinricPro WebSockets vs MQTT
layout: post
lang: en
---

# Does SinricPro use MQTT?

Nope! SinricPro uses WebSockets (WSS) instead.

# Why? 

Because WebSockets give you fast, real-time communication — perfect for things like voice commands, where every millisecond counts. Our tests showed that WebSockets are super reliable and work great even through firewalls.

With SinricPro, your ESP8266/ESP32 devices connect directly to the cloud using WebSockets. The SDK handles all the messy details (connection + authentication) for you.

If you really want MQTT, you’ll need to run your own broker and set up custom logic — it’s not part of SinricPro by default.

# Is mqtt.sinric.pro available ?

Nope! There's no mqtt.sinric.pro