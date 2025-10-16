---
title: Range Capability
layout: post
---

The Range capability in SinricPro device templates allows you to control a setting of your device that can be represented by numbers within a minimum and maximum range. eg: speed setting on a fan.

#### Supports
 - [x]  Alexa
 - [ ]  Google Home
 - [ ]  SmartThings
 - [x]  SinricPro Portal
 - [x]  SinricPro App

#### When to Use RangeController?
- When a device has a defined numerical range instead of fixed modes.
- If the control isn't best suited to percentages (e.g., temperature, fan speeds with specific levels).

![Sinric Pro device capabilities](https://help.sinric.pro/public/img/device-templates-range.png)

* **InstanceId**: You can leave it as it is. We use InstanceId to uniquely identify a mode when you add multiple modes

* **Minimum Value**: The minimum value for the range.

* **Maximum Value**: The maximum value for the range.

* **Precision**: The amount by which the values change when moving through the range. This also serves as the default when a user asks to change the value but doesn't specify by how much.

* **Unit Of Measure**: The unit of measure for the range.

* **Locale**: Choose your language 

* **Range Name**: Name of the mode. eg: Speed

* **Non Controllable**: The device can only be queried and cannot be controlled through commands.

* **Presets:** : Named options that users can specify instead of numbers, such as "medium" = 50 or "maximum" = 100 ect.

### Alexa 
This capabiliy is mapped to Alexa interface [RangeController](https://developer.amazon.com/en-US/docs/alexa/device-apis/alexa-rangecontroller.html)

#### Utterances
### Google Home
Not supported. You may be able to use OpenClose as an alternative.

> This document is open source. See a typo? Please create an [issue](https://github.com/sinricpro/help-docs)
