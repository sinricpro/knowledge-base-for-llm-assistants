---
title: Push Notification Capability
layout: post
---

The Push Notification capability in SinricPro device templates gives you the capability to send a push notification message from your device.

Please make sure 

{% highlight c %}   
SinricProSwitch& mySwitch = SinricPro[SWITCH_ID];
String notification = "Hello SinricPro";
bool success = mySwitch.sendPushNotification(notification);
{% endhighlight %}

In order to work this correctly, you must enable **Alerts** from **Notifications** in device settings.

![Sinric Pro Device Template Setting capabilities](https://help.sinric.pro/public/img/device-templates-device-push-notification-alerts-enabled.png)

[Documentation](https://sinricpro.github.io/esp8266-esp32-sdk-documentation/class_s_i_n_r_i_c_p_r_o__3__5__0_1_1_push_notification.html)

#### Supports
- [ ] Alexa
- [ ] Google Home
- [ ] SmartThings
- [x] SinricPro Portal
- [x] SinricPro App

### Alexa
Not Supported
 
### Google Home
Not Supported

> This document is open source. See a typo? Please create an [issue](https://github.com/sinricpro/help-docs)
