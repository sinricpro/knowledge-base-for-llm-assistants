# Light Example

## Overview
This example demonstrates how to integrate a smart light device with SinricPro. It provides comprehensive control over lighting, including basic on/off functionality, adjustable brightness (absolute and relative), RGB color control, and color temperature adjustments (setting absolute values and incremental changes). This example is suitable for developers looking to create smart RGBW or tunable white light fixtures.

## Key Concepts/Capabilities Demonstrated
*   **Device Type**: `SinricProLight`
*   **Capabilities Used**: 
    *   [`PowerStateController`]: For managing the light's on/off state.
    *   [`BrightnessController`]: For setting and adjusting the light's brightness.
    *   [`ColorController`]: For setting the light's RGB color.
    *   [`ColorTemperatureController`]: For setting and adjusting the light's color temperature.
*   **SinricPro Integration**: Demonstrates how to initialize SinricPro, register device-specific callbacks, and maintain the connection.
*   **WiFi Connectivity**: Standard ESP-based WiFi connection setup.
*   **Data Structures**: Usage of `std::map` for efficient color temperature lookup.

## Hardware Requirements
*   An ESP8266 or ESP32 development board.
*   (Implied) A smart light that supports on/off, dimming, RGB color, and tunable white (color temperature). This could be an RGBW LED strip with a suitable driver (e.g., PWM outputs).

## Setup Instructions
1.  **Install Libraries**: Ensure you have the SinricPro library and its dependencies installed. Refer to the main SDK documentation for installation instructions.
2.  **Update Credentials**: Modify the following `#define` statements in the `Light.ino` file:
    *   `WIFI_SSID`: Your Wi-Fi network SSID.
    *   `WIFI_PASS`: Your Wi-Fi network password.
    *   `APP_KEY`: Your SinricPro App Key (from the SinricPro portal).
    *   `APP_SECRET`: Your SinricPro App Secret (from the SinricPro portal).
    *   `LIGHT_ID`: The Device ID of your Light, created in the SinricPro portal (ensure it's of type `LIGHT`).
3.  **Baud Rate**: Adjust `BAUD_RATE` if needed for your serial monitor.
4.  **Upload Sketch**: Upload the `Light.ino` sketch to your ESP8266/ESP32 board.
5.  **Monitor Serial**: Open the serial monitor to observe connection status and command logs (uncomment `#define ENABLE_DEBUG` for more verbose output).

## Code Walkthrough

### Global State Struct
```cpp
struct {
  bool powerState = false;
  int brightness = 0;
  struct {
    byte r = 0;
    byte g = 0;
    byte b = 0;
  } color;
  int colorTemperature = colorTemperatureArray[0]; 
} device_state; 
```
This struct holds the current power state, brightness, RGB color, and color temperature of the light. These values are updated by incoming SinricPro commands and should be used to control your physical light.

### Color Temperature Lookup
```cpp
int colorTemperatureArray[] = {2200, 2700, 4000, 5500, 7000};  
int max_color_temperatures = sizeof(colorTemperatureArray) / sizeof(colorTemperatureArray[0]);
std::map<int, int> colorTemperatureIndex;

void setupColorTemperatureIndex() {
  Serial.printf("Setup color temperature lookup table\r\n");
  for (int i=0;i < max_color_temperatures; i++) {
    colorTemperatureIndex[colorTemperatureArray[i]] = i;
    Serial.printf("colorTemperatureIndex[%i] = %i\r\n", colorTemperatureArray[i], colorTemperatureIndex[colorTemperatureArray[i]]);
  }
}
```
These define an array of supported color temperatures and a `std::map` (`colorTemperatureIndex`) to quickly look up the array index for a given color temperature value. `setupColorTemperatureIndex()` initializes this map.

### Callbacks
These functions are invoked when a corresponding command is received from the SinricPro server.

#### `onPowerState(const String &deviceId, bool &state)`
Handles `setPowerState` commands (on/off).
```cpp
bool onPowerState(const String &deviceId, bool &state) {
  Serial.printf("Device %s power turned %s \r\n", deviceId.c_str(), state?"on":"off");
  device_state.powerState = state;
  return true; 
}
```

#### `onBrightness(const String &deviceId, int &brightness)`
Handles `setBrightness` commands.
```cpp
bool onBrightness(const String &deviceId, int &brightness) {
  device_state.brightness = brightness;
  Serial.printf("Device %s brightness level changed to %d\r\n", deviceId.c_str(), brightness);
  return true;
}
```

#### `onAdjustBrightness(const String &deviceId, int brightnessDelta)`
Handles `adjustBrightness` commands, calculating the new absolute brightness.
```cpp
bool onAdjustBrightness(const String &deviceId, int brightnessDelta) {
  device_state.brightness += brightnessDelta;
  Serial.printf("Device %s brightness level changed about %i to %d\r\n", deviceId.c_str(), brightnessDelta, device_state.brightness);
  brightnessDelta = device_state.brightness;
  return true;
}
```

#### `onColor(const String &deviceId, byte &r, byte &g, byte &b)`
Handles `setColor` commands.
```cpp
bool onColor(const String &deviceId, byte &r, byte &g, byte &b) {
  device_state.color.r = r;
  device_state.color.g = g;
  device_state.color.b = b;
  Serial.printf("Device %s color changed to %d, %d, %d (RGB)\r\n", deviceId.c_str(), device_state.color.r, device_state.color.g, device_state.color.b);
  return true;
}
```

#### `onColorTemperature(const String &deviceId, int &colorTemperature)`
Handles `setColorTemperature` commands.
```cpp
bool onColorTemperature(const String &deviceId, int &colorTemperature) {
  device_state.colorTemperature = colorTemperature;
  Serial.printf("Device %s color temperature changed to %d\r\n", deviceId.c_str(), device_state.colorTemperature);
  return true;
}
```

#### `onIncreaseColorTemperature(const String &deviceId, int &colorTemperature)`
Handles `increaseColorTemperature` commands, incrementing the color temperature based on the predefined array.
```cpp
bool onIncreaseColorTemperature(const String &deviceId, int &colorTemperature) {
  int index = colorTemperatureIndex[device_state.colorTemperature];              
  index++;                                                                
  if (index < 0) index = 0;                                               
  if (index > max_color_temperatures-1) index = max_color_temperatures-1; 
  device_state.colorTemperature = colorTemperatureArray[index];                  
  Serial.printf("Device %s increased color temperature to %d\r\n", deviceId.c_str(), device_state.colorTemperature);
  colorTemperature = device_state.colorTemperature;                              
  return true;
}
```

#### `onDecreaseColorTemperature(const String &deviceId, int &colorTemperature)`
Handles `decreaseColorTemperature` commands, decrementing the color temperature based on the predefined array.
```cpp
bool onDecreaseColorTemperature(const String &deviceId, int &colorTemperature) {
  int index = colorTemperatureIndex[device_state.colorTemperature];              
  index--;                                                                
  if (index < 0) index = 0;                                               
  if (index > max_color_temperatures-1) index = max_color_temperatures-1; 
  device_state.colorTemperature = colorTemperatureArray[index];                  
  Serial.printf("Device %s decreased color temperature to %d\r\n", deviceId.c_str(), device_state.colorTemperature);
  colorTemperature = device_state.colorTemperature;                              
  return true;
}
```

### `setupWiFi()`
Connects the ESP board to your specified Wi-Fi network.
```cpp
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
```

### `setupSinricPro()`
Initializes the SinricPro library and registers all the necessary callbacks for the Light device.
```cpp
void setupSinricPro() {
  SinricProLight &myLight = SinricPro[LIGHT_ID];
  myLight.onPowerState(onPowerState);
  myLight.onBrightness(onBrightness);
  myLight.onAdjustBrightness(onAdjustBrightness);
  myLight.onColor(onColor);
  myLight.onColorTemperature(onColorTemperature);
  myLight.onIncreaseColorTemperature(onIncreaseColorTemperature);
  myLight.onDecreaseColorTemperature(onDecreaseColorTemperature);

  SinricPro.onConnected([](){ Serial.printf("Connected to SinricPro\r\n"); }); 
  SinricPro.onDisconnected([](){ Serial.printf("Disconnected from SinricPro\r\n"); });
  SinricPro.begin(APP_KEY, APP_SECRET);
}
```

### `setup()` and `loop()`
Standard Arduino functions for initialization and continuous execution.
```cpp
void setup() {
  Serial.begin(BAUD_RATE); Serial.printf("\r\n\r\n");
  setupColorTemperatureIndex(); 
  setupWiFi();
  setupSinricPro();
}

void loop() {
  SinricPro.handle();
}
```

The `loop()` function continuously calls `SinricPro.handle()` to process SinricPro events and maintain the connection.

## Dependencies
*   `Arduino.h`
*   `ESP8266WiFi.h` (for ESP8266) or `WiFi.h` (for ESP32/RP2040)
*   `SinricPro.h`
*   `SinricProLight.h`
*   `<map>` (for `std::map`)

## Tags
`SinricPro`, `Example`, `Light`, `Smart Home`, `PowerState`, `Brightness`, `Color`, `Color Temperature`, `RGB`, `Tunable White`, `ESP8266`, `ESP32`
