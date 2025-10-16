---
title: Start-Stop Capability
layout: post
---

The Start-Stop capability in SinricPro device templates allows devices to start and stop operations. While similar to power on/off functionality, this capability addresses more complex operational states. For example, some washing machines can be powered on before actually starting their wash cycle.

Beyond simple starting and stopping, this capability can include pausing functionality, where a device temporarily halts operation while maintaining its current state. When resumed, the device continues from where it paused, unlike starting/restarting which begins operation from the beginning regardless of previous state.

![Sinric Pro Device Template Start Stop](https://help.sinric.pro/public/img/device-templates-start-stop-pause.png)

#### Supports
- [ ] Alexa
- [x] Google Home
- [ ] SmartThings
- [x] SinricPro Portal
- [x] SinricPro App

### Alexa
Not Supported 

### Google Home
This capability is mapped to Google Home [StartStop](https://developers.home.google.com/cloud-to-cloud/traits/startstop) trait

#### Utterances
> This document is open source. See a typo? Please create an [issue](https://github.com/sinricpro/help-docs)
