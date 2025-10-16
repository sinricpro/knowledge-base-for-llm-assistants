---
title: Mode Capability
layout: post
---

The Mode capability in SinricPro device templates allows you to control the mode settings of your device where a device can operate in different predefined modes instead of just ON/OFF states. 

For example, you can use Mode capability for:

- Fan speed settings (low, medium, high)
- Air conditioner modes (cool, heat, fan-only, auto)
- Washing machine modes (delicate, normal, heavy-duty)
- Smart light modes (reading, night, party)

Each mode has multiple possible settings, but only one can be selected at a time; eg: a dryer cannot be in "delicate," "normal," and "heavy duty" mode simultaneously

#### Supports
 - [x]  Alexa
 - [x]  Google Home
 - [ ]  SmartThings
 - [x]  SinricPro Portal
 - [x]  SinricPro App

![Sinric Pro device capabilities](https://help.sinric.pro/public/img/device-templates-modes.png)

* **InstanceId**: You can leave it as it is. We use InstanceId to uniquely identify a mode when you add multiple modes

* **Locale**: Choose your language 

* **Mode name**: Name of the mode. eg: Speed

* **Non Controllable**: The device can only be queried and cannot be controlled through commands.

* **Modes**: List of values for mode. eg: low, medium, high

### Alexa 
This capabiliy is mapped to Alexa interface [ModeController](https://developer.amazon.com/en-US/docs/alexa/device-apis/alexa-modecontroller.html)

#### Utterances
### Google Home
This capability is mapped to Google Home [modes](https://developers.home.google.com/cloud-to-cloud/traits/modes) trait

#### Utterances
 > This document is open source. See a typo? Please create an [issue](https://github.com/sinricpro/help-docs)
