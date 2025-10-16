---
title: Color Temperature Capability
layout: post
---

The Color Temperature capability in SinricPro device templates allows you to control the color temperature of smart lighting devices. This feature lets users adjust their lights from warm (yellowish) to cool (bluish) white tones, measured in Kelvin units (typically ranging from 2200K to 7000K), enabling customization of lighting ambiance for different activities and preferences.

#### Supports
 - [x]  Alexa
 - [x]  Google Home
 - [ ]  SmartThings
 - [x]  SinricPro Portal
 - [x]  SinricPro App

### Alexa 
This capabiliy is mapped to Alexa interface [ColorTemperatureController](https://developer.amazon.com/en-US/docs/alexa/device-apis/alexa-colortemperaturecontroller.html)

#### Utterances
#### Shade of White and Color Temperature used by Alexa
| Shade of White | Color Temperature in Absolute Kelvin |
|----------------|--------------------------------------|
| warm, warm white | 2200 |
| incandescent, soft white | 2700 |
| white | 4000 |
| daylight, daylight white | 5500 |
| cool, cool white | 7000 |

### Google Home
This capability is mapped to Google Home [ColorSetting](https://developers.home.google.com/cloud-to-cloud/traits/colorsetting) trait

#### Utterances
#### Temperature and Color Name used by GoogleHome
| Temperature (Kelvin) | Color Name |
|----------------------|------------|
| 2000 | Candle Light |
| 2500 | Ultra Warm White |
| 3000 | Soft White, Morning White, Reading White |
| 4000 | Cool White |
| 5000 | Day Light, White |
| 6000 | Floral White |
| 7000 | Cloudy Day Light, White Smoke |
| 8000 | Blue Overcast |
| 9000 | Blue Sky |

> This document is open source. See a typo? Please create an [issue](https://github.com/sinricpro/help-docs)
