# RGB_LED_Stripe_5050 Example

## Overview
This example demonstrates how to control a common anode/cathode RGB LED strip (e.g., 5050 type) using PWM outputs from an ESP board and integrate it with SinricPro. It provides comprehensive lighting controls including on/off, brightness (absolute and relative), RGB color, and color temperature adjustments. This is ideal for creating smart ambient lighting with tunable white capabilities.

## Key Concepts/Capabilities Demonstrated
*   **Device Type**: `SinricProLight`
*   **Capabilities Used**:
    *   [`PowerStateController`]: For managing the LED strip's on/off state.
    *   [`BrightnessController`]: For setting and adjusting the LED strip's overall brightness.
    *   [`ColorController`]: For setting the LED strip's RGB color.
    *   [`ColorTemperatureController`]: For setting and adjusting the LED strip's color temperature.
*   **PWM Control**: Direct control of RGB LED channels using Pulse Width Modulation.
*   **Color Temperature Mapping**: Using a `std::map` to convert color temperature values to corresponding RGB values.
*   **SinricPro Integration**: Demonstrates how to initialize SinricPro, register device-specific callbacks, and maintain the connection.
*   **WiFi Connectivity**: Standard ESP-based WiFi connection setup.

## Hardware Requirements
*   An ESP8266 or ESP32 development board.
*   RGB LED strip (common anode/cathode, e.g., 5050 type).
*   3 MOSFETs (or a suitable RGB LED driver module) to control the high current of the LED strip. *Note: Direct connection of LED strip to ESP pins is not recommended due to current limitations.*
*   Proper power supply for the LED strip (matching its voltage and current requirements).

## Setup Instructions
1.  **Install Libraries**: Ensure you have the SinricPro library and its dependencies installed. Refer to the main SDK documentation for installation instructions.
2.  **Update Credentials**: Modify the following `#define` statements in the `RGB_LED_Stripe_5050.ino` file:
    *   `WIFI_SSID`: Your Wi-Fi network SSID.
    *   `WIFI_PASS`: Your Wi-Fi network password.
    *   `APP_KEY`: Your SinricPro App Key (from the SinricPro portal).
    *   `APP_SECRET`: Your SinricPro App Secret (from the SinricPro portal).
    *   `LIGHT_ID`: The Device ID of your Light, created in the SinricPro portal (ensure it's of type `LIGHT`).
3.  **Pin Configuration**: Adjust `RED_PIN`, `GREEN_PIN`, `BLUE_PIN` to the GPIO pins connected to your MOSFETs/driver for the respective color channels.
4.  **Baud Rate**: Adjust `BAUD_RATE` if needed for your serial monitor.
5.  **Wiring**: Connect your RGB LED strip to the ESP board via MOSFETs/driver as per the pin definitions. Ensure all connections are secure and power is supplied correctly.
6.  **Upload Sketch**: Upload the `RGB_LED_Stripe_5050.ino` sketch to your ESP8266/ESP32 board.
7.  **Monitor Serial**: Open the serial monitor to observe connection status and command logs (uncomment `#define ENABLE_DEBUG` for more verbose output).

## Code Walkthrough

### Defines and Pin Assignments
```cpp
#define WIFI_SSID  "YOUR-WIFI-SSID"
#define WIFI_PASS  "YOUR-WIFI-PASS"
#define APP_KEY    "YOUR-APPKEY"     
#define APP_SECRET "YOUR-APPSECRET"  
#define LIGHT_ID   "YOUR-DEVICEID"   
#define BAUD_RATE  115200              

#define BLUE_PIN  D5  // PIN for BLUE Mosfet  - change this to your need (D5 = GPIO14 on ESP8266)
#define RED_PIN   D6  // PIN for RED Mosfet   - change this to your need (D6 = GPIO12 on ESP8266)
#define GREEN_PIN D7  // PIN for GREEN Mosfet - change this to your need (D7 = GPIO13 on ESP8266)
```
These define your Wi-Fi and SinricPro credentials, the light device ID, serial baud rate, and the GPIO pins connected to the RGB channels of your LED strip.

### Color and Device State Structures
```cpp
struct Color {
    uint8_t r;
    uint8_t g;
    uint8_t b;
};

// Colortemperature lookup table
using ColorTemperatures = std::map<uint16_t, Color>;
ColorTemperatures colorTemperatures{
    //   {Temperature value, {color r, g, b}}
    {2000, {255, 138, 18}},
    // ... (other color temperature entries)
    {9000, {214, 225, 255}}};

struct DeviceState {
    bool  powerState       = false;
    Color color            = colorTemperatures[9000];
    int   colorTemperature = 9000;
    int   brightness       = 100;
} device_state;

SinricProLight& myLight = SinricPro[LIGHT_ID];  
```
`Color` struct holds RGB values. `ColorTemperatures` is a `std::map` that defines predefined RGB values for various color temperatures, allowing the light to simulate different white tones. `DeviceState` struct stores the current power state, RGB color, color temperature, and brightness of the light. `myLight` is the global instance of `SinricProLight`.

### `setStripe()`
This crucial function translates the `DeviceState` into PWM values for the RGB pins. It handles the power state (turning off by setting pins LOW) and applies brightness mapping to the RGB values before writing them to the analog pins using `analogWrite()`. 
```cpp
void setStripe() {
    int rValue = map(device_state.color.r * device_state.brightness, 0, 255 * 100, 0, 1023);  
    int gValue = map(device_state.color.g * device_state.brightness, 0, 255 * 100, 0, 1023);  
    int bValue = map(device_state.color.b * device_state.brightness, 0, 255 * 100, 0, 1023);  

    if (device_state.powerState == false) {  
        digitalWrite(RED_PIN, LOW);          
        digitalWrite(GREEN_PIN, LOW);        
        digitalWrite(BLUE_PIN, LOW);         
    } else {
        analogWrite(RED_PIN, rValue);    
        analogWrite(GREEN_PIN, gValue);  
        analogWrite(BLUE_PIN, bValue);   
    }
}
```

### Callbacks
These functions are invoked when a corresponding command is received from the SinricPro server.

#### `onPowerState(const String& deviceId, bool& state)`
Handles `setPowerState` commands (on/off). Updates `device_state.powerState` and calls `setStripe()`. 
```cpp
bool onPowerState(const String& deviceId, bool& state) {
    device_state.powerState = state;  
    setStripe();                      
    return true;
}
```

#### `onBrightness(const String& deviceId, int& brightness)`
Handles `setBrightness` commands. Updates `device_state.brightness` and calls `setStripe()`. 
```cpp
bool onBrightness(const String& deviceId, int& brightness) {
    device_state.brightness = brightness;  
    setStripe();                           
    return true;
}
```

#### `onAdjustBrightness(const String& deviceId, int& brightnessDelta)`
Handles `adjustBrightness` commands. Adjusts `device_state.brightness` and calls `setStripe()`. 
```cpp
bool onAdjustBrightness(const String& deviceId, int& brightnessDelta) {
    device_state.brightness += brightnessDelta;  
    brightnessDelta = device_state.brightness;   
    setStripe();                                 
    return true;
}
```

#### `onColor(const String& deviceId, byte& r, byte& g, byte& b)`
Handles `setColor` commands. Updates `device_state.color` and calls `setStripe()`. 
```cpp
bool onColor(const String& deviceId, byte& r, byte& g, byte& b) {
    device_state.color.r = r;  
    device_state.color.g = g;  
    device_state.color.b = b;  
    setStripe();               
    return true;
}
```

#### `onColorTemperature(const String& deviceId, int& colorTemperature)`
Handles `setColorTemperature` commands. Looks up the RGB values for the requested color temperature from `colorTemperatures` map, updates `device_state.color` and `device_state.colorTemperature`, then calls `setStripe()`. 
```cpp
bool onColorTemperature(const String& deviceId, int& colorTemperature) {
    device_state.color            = colorTemperatures[colorTemperature];  
    device_state.colorTemperature = colorTemperature;                     
    setStripe();                                                          
    return true;
}
```

#### `onIncreaseColorTemperature(const String& devceId, int& colorTemperature)`
Handles `increaseColorTemperature` requests. Finds the next higher color temperature in the `colorTemperatures` map and applies it.
```cpp
bool onIncreaseColorTemperature(const String& devceId, int& colorTemperature) {
    auto current = colorTemperatures.find(device_state.colorTemperature);  
    auto next    = std::next(current);                                     
    if (next == colorTemperatures.end()) next = current;                   
    device_state.color            = next->second;                          
    device_state.colorTemperature = next->first;                           
    colorTemperature              = device_state.colorTemperature;         
    setStripe();
    return true;
}
```

#### `onDecreaseColorTemperature(const String& devceId, int& colorTemperature)`
Handles `decreaseColorTemperature` requests. Finds the next lower color temperature in the `colorTemperatures` map and applies it.
```cpp
bool onDecreaseColorTemperature(const String& devceId, int& colorTemperature) {
    auto current = colorTemperatures.find(device_state.colorTemperature);  
    auto next    = std::prev(current);                                     
    if (next == colorTemperatures.end()) next = current;                   
    device_state.color            = next->second;                          
    device_state.colorTemperature = next->first;                           
    colorTemperature              = device_state.colorTemperature;         
    setStripe();
    return true;
}
```

### `setupWiFi()`
Connects the ESP board to your specified Wi-Fi network.
```cpp
void setupWiFi() {
    Serial.printf("WiFi: connecting");
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
    Serial.printf("connected\r\nIP is %s\r\n", WiFi.localIP().toString().c_str());
}
```

### `setupSinricPro()`
Initializes the SinricPro library and registers all the necessary callbacks for the Light device.
```cpp
void setupSinricPro() {
    myLight.onPowerState(onPowerState);
    myLight.onBrightness(onBrightness);
    myLight.onAdjustBrightness(onAdjustBrightness);
    myLight.onColor(onColor);
    myLight.onColorTemperature(onColorTemperature);
    myLight.onDecreaseColorTemperature(onDecreaseColorTemperature);
    myLight.onIncreaseColorTemperature(onIncreaseColorTemperature);

    SinricPro.begin(APP_KEY, APP_SECRET);
}
```

### `setup()` and `loop()`
Standard Arduino functions for initialization and continuous execution.
```cpp
void setup() {
    Serial.begin(BAUD_RATE);  
    pinMode(RED_PIN, OUTPUT);    
    pinMode(GREEN_PIN, OUTPUT);  
    pinMode(BLUE_PIN, OUTPUT);   

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
*   `<map>`

## Tags
`SinricPro`, `Example`, `Light`, `RGB LED`, `5050 LED Strip`, `PWM`, `Color Temperature`, `Smart Home`, `ESP8266`, `ESP32`
