---
title: onPowerState for Air Quality, Contact, Motion, and Temperature Sensors
layout: post
lang: en
---

Starting with **SinricPro SDK v3.0.0** for ESP8266, ESP32, and Rpi(RP2040), we have **removed the `onPowerState`** capability.

- Air Quality Sensor  
- Contact Sensor  
- Motion Sensor  
- Temperature Sensor  


# Why was this change made?

1. **Security Considerations** – Allowing power control on passive sensors introduces unnecessary risk.
2. **Technical Accuracy** – Sensors are typically always-on devices and do not require explicit power on/off control.

# What if I still need PowerState functionality?

We understand some users rely on this feature for custom use cases. While officially deprecated, you can still access it via the following methods:

## Option 1: Downgrade to SDK v2.11.1

You may downgrade to version **2.11.1**, where `PowerStateController` is still available.

[View Changelog & Download v2.11.1](https://github.com/sinricpro/esp8266-esp32-sdk/blob/master/changelog.md)

Note: Downgrading may mean missing out on newer features, bug fixes, and security updates.

## Option 2: Create a Custom Device Template

Build a **custom device template** that combines a Contact Sensor (or other sensor type) with the `Power` capability.

This approach gives you full control while keeping your project on the latest SDK.

---

**Recommendation**: Use Option 2 for long-term compatibility and security.
