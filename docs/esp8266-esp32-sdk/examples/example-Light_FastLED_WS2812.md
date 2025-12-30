# Light_FastLED_WS2812 Example

## Overview
This example demonstrates how to control a WS2812B RGB LED strip using the popular FastLED library and integrate it with SinricPro. It provides functionalities for turning the LED strip on/off, setting and adjusting its brightness, and setting its RGB color. This is ideal for creating smart ambient lighting, decorative lighting, or notification systems.

## Key Concepts/Capabilities Demonstrated
*   **Device Type**: `SinricProLight`
*   **Capabilities Used**: 
    *   [`PowerStateController`]: For managing the LED strip's on/off state.
    *   [`BrightnessController`]: For setting and adjusting the LED strip's overall brightness.
    *   [`ColorController`]: For setting the LED strip's RGB color.
*   **FastLED Library**: Integration with the FastLED library for efficient control of addressable LEDs like WS2812B.
*   **SinricPro Integration**: Demonstrates how to initialize SinricPro, register device-specific callbacks, and maintain the connection.
*   **WiFi Connectivity**: Standard ESP-based WiFi connection setup.

## Hardware Requirements
*   An ESP8266 or ESP32 development board.
*   WS2812B RGB LED strip (e.g., NeoPixel).
*   **Wiring**: Connect the data pin of your WS2812B strip to the `LED_PIN` defined in the sketch. Ensure proper power supply (5V) and ground connections for the LED strip, potentially with an external power supply if using many LEDs.

## Setup Instructions
1.  **Install Libraries**: Ensure you have the SinricPro library and its dependencies installed. Install the `FastLED` library (available in the Arduino Library Manager).
2.  **Update Credentials**: Modify the following `#define` statements in the `Light_FastLED_WS2812.ino` file:
    *   `WIFI_SSID`: Your Wi-Fi network SSID.
    *   `WIFI_PASS`: Your Wi-Fi network password.
    *   `APP_KEY`: Your SinricPro App Key (from the SinricPro portal).
    *   `APP_SECRET`: Your SinricPro App Secret (from the SinricPro portal).
    *   `LIGHT_ID`: The Device ID of your Light, created in the SinricPro portal (ensure it's of type `LIGHT`).
3.  **LED Configuration**: Adjust `NUM_LEDS` to the total number of LEDs on your strip and `LED_PIN` to the GPIO pin connected to the data line of your WS2812B strip.
4.  **Baud Rate**: Adjust `BAUD_RATE` if needed for your serial monitor.
5.  **Upload Sketch**: Upload the `Light_FastLED_WS2812.ino` sketch to your ESP8266/ESP32 board.
6.  **Monitor Serial**: Open the serial monitor to observe connection status and command logs (uncomment `#define ENABLE_DEBUG` for more verbose output).

## Code Walkthrough

### Defines and LED Configuration
```cpp
#define WIFI_SSID         "YOUR-WIFI-SSID"    
#define WIFI_PASS         "YOUR-WIFI-PASSWORD" 
#define APP_KEY           "YOUR-APP-KEY"      
#define APP_SECRET        "YOUR-APP-SECRET"   
#define LIGHT_ID          "YOUR-DEVICE-ID"    
#define BAUD_RATE         115200                

#define NUM_LEDS          1                   // how much LEDs are on the stripe
#define LED_PIN           3                   // LED stripe is connected to PIN 3

bool powerState;        
int globalBrightness = 100;
CRGB leds[NUM_LEDS];
```
These define your Wi-Fi and SinricPro credentials, the light device ID, serial baud rate, and parameters for the LED strip (`NUM_LEDS`, `LED_PIN`). `powerState` and `globalBrightness` store the current state, and `leds` is the FastLED array to hold LED color data.

### Callbacks
These functions are invoked when a corresponding command is received from the SinricPro server.

#### `onPowerState(const String &deviceId, bool &state)`
Handles `setPowerState` commands (on/off). It sets the overall brightness of the LED strip to 0 (off) or to the `globalBrightness` (on).
```cpp
bool onPowerState(const String &deviceId, bool &state) {
  powerState = state;
  if (state) {
    FastLED.setBrightness(map(globalBrightness, 0, 100, 0, 255));
  } else {
    FastLED.setBrightness(0);
  }
  FastLED.show();
  return true; 
}
```

#### `onBrightness(const String &deviceId, int &brightness)`
Handles `setBrightness` commands. It maps the 0-100 SinricPro brightness to FastLED's 0-255 scale and updates the LED strip.
```cpp
bool onBrightness(const String &deviceId, int &brightness) {
  globalBrightness = brightness;
  FastLED.setBrightness(map(brightness, 0, 100, 0, 255));
  FastLED.show();
  return true;
}
```

#### `onAdjustBrightness(const String &deviceId, int brightnessDelta)`
Handles `adjustBrightness` commands. It adjusts the `globalBrightness` and updates the LED strip accordingly.
```cpp
bool onAdjustBrightness(const String &deviceId, int brightnessDelta) {
  globalBrightness += brightnessDelta;
  brightnessDelta = globalBrightness;
  FastLED.setBrightness(map(globalBrightness, 0, 100, 0, 255));
  FastLED.show();
  return true;
}
```

#### `onColor(const String &deviceId, byte &r, byte &g, byte &b)`
Handles `setColor` commands. It sets all LEDs on the strip to the specified RGB color.
```cpp
bool onColor(const String &deviceId, byte &r, byte &g, byte &b) {
  fill_solid(leds, NUM_LEDS, CRGB(r, g, b));
  FastLED.show();
  return true;
}
```

### `setupFastLED()`
Initializes the FastLED library, specifies the LED type (WS2812B), data pin, and color order. It also sets an initial brightness and color for the LEDs.
```cpp
void setupFastLED() {
  FastLED.addLeds<WS2812B, LED_PIN, RGB>(leds, NUM_LEDS);
  FastLED.setBrightness(map(globalBrightness, 0, 100, 0, 255));
  fill_solid(leds, NUM_LEDS, CRGB::White);
  FastLED.show();
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
  Serial.printf("connected!\r\n[WiFi]: IP-Address is %s\r\n", WiFi.localIP().toString().c_str());
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
  setupFastLED();
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
*   `FastLED.h`
*   `SinricPro.h`
*   `SinricProLight.h`

## Tags
`SinricPro`, `Example`, `Light`, `WS2812B`, `FastLED`, `RGB LED`, `Smart Home`, `PowerState`, `Brightness`, `Color`, `ESP8266`, `ESP32`
