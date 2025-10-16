# TV Example

## Overview
This example demonstrates how to integrate a smart TV device with SinricPro, providing extensive control over its functionalities. It covers power state, volume control (set and adjust), mute/unmute, media playback controls (Play, Pause, Stop, etc.), input selection, and comprehensive channel management (change by name, change by number, skip channels). This is a comprehensive example for building a smart TV remote or integrating a TV into a smart home system.

## Key Concepts/Capabilities Demonstrated
*   **Device Type**: `SinricProTV`
*   **Capabilities Used**: 
    *   [`PowerStateController`]: For managing the TV's on/off state.
    *   [`VolumeController`]: For setting and adjusting the TV's volume.
    *   [`MuteController`]: For muting and unmuting the TV.
    *   [`MediaController`]: For controlling media playback (Play, Pause, Stop, Next, Previous, FastForward, Rewind, StartOver).
    *   [`InputController`]: For selecting different audio/video input sources.
    *   [`ChannelController`]: For changing channels by name or number, and skipping channels.
*   **SinricPro Integration**: Demonstrates how to initialize SinricPro, register numerous device-specific callbacks, and maintain the connection.
*   **WiFi Connectivity**: Standard ESP-based WiFi connection setup.
*   **Data Structures**: Usage of `std::map` for efficient channel name to number lookup.

## Hardware Requirements
*   An ESP8266 or ESP32 development board.
*   (Implied) A TV that can be controlled by the microcontroller (e.g., via IR commands, HDMI-CEC, or serial commands to the TV's control port).

## Setup Instructions
1.  **Install Libraries**: Ensure you have the SinricPro library and its dependencies installed. Refer to the main SDK documentation for installation instructions.
2.  **Update Credentials**: Modify the following `#define` statements in the `TV.ino` file:
    *   `WIFI_SSID`: Your Wi-Fi network SSID.
    *   `WIFI_PASS`: Your Wi-Fi network password.
    *   `APP_KEY`: Your SinricPro App Key (from the SinricPro portal).
    *   `APP_SECRET`: Your SinricPro App Secret (from the SinricPro portal).
    *   `TV_ID`: The Device ID of your TV, created in the SinricPro portal (ensure it's of type `TV`).
3.  **Configure Channels**: Update the `channelNames` array with the names of your TV channels in the desired order. The index of the array corresponds to the channel number (starting from 0).
4.  **Baud Rate**: Adjust `BAUD_RATE` if needed for your serial monitor.
5.  **Upload Sketch**: Upload the `TV.ino` sketch to your ESP8266/ESP32 board.
6.  **Monitor Serial**: Open the serial monitor to observe connection status and command logs (uncomment `#define ENABLE_DEBUG` for more verbose output).

## Code Walkthrough

### Defines and Global State
```cpp
#define WIFI_SSID         "YOUR-WIFI-SSID"    
#define WIFI_PASS         "YOUR-WIFI-PASSWORD" 
#define APP_KEY           "YOUR-APP-KEY"      
#define APP_SECRET        "YOUR-APP-SECRET"   
#define TV_ID             "YOUR-DEVICE-ID"    
#define BAUD_RATE         115200

bool tvPowerState;
unsigned int tvVolume;
unsigned int tvChannel;
bool tvMuted;
```
These define your Wi-Fi and SinricPro credentials, the TV device ID, serial baud rate. Global variables `tvPowerState`, `tvVolume`, `tvChannel`, and `tvMuted` store the current state of the TV.

### Channel Configuration
```cpp
const char* channelNames[] = { 
  "A/V", "ard", "ZDF", "n. d. r.", 
  "kabel eins", "VOX", "Sat.1", "ProSieben", 
  "rtl", "RTL II", "SUPER RTL", "KiKA"
};
#define MAX_CHANNELS sizeof(channelNames) / sizeof(channelNames[0])
std::map<String, unsigned int> channelNumbers;

void setupChannelNumbers() {
  for (unsigned int i=0; i < MAX_CHANNELS; i++) {
    channelNumbers[channelNames[i]] = i;
  }
}
```
`channelNames` is an array of your TV channel names. `MAX_CHANNELS` calculates its size. `channelNumbers` is a `std::map` that provides a lookup from channel name to its numerical index, initialized in `setupChannelNumbers()`.

### Callbacks
These functions are invoked when a corresponding command is received from the SinricPro server.

#### `onAdjustVolume(const String &deviceId, int &volumeDelta, bool volumeDefault)`
Handles `adjustVolume` commands. Adjusts `tvVolume` and returns the new absolute volume.
```cpp
bool onAdjustVolume(const String &deviceId, int &volumeDelta, bool volumeDefault) {
  tvVolume += volumeDelta;  
  Serial.printf("Volume changed about %i to %i\r\n", volumeDelta, tvVolume);
  volumeDelta = tvVolume; 
  return true;
}
```

#### `onChangeChannel(const String &deviceId, String &channel)`
Handles `changeChannel` requests by channel name. Updates `tvChannel` based on the channel name lookup.
```cpp
bool onChangeChannel(const String &deviceId, String &channel) {
  tvChannel = channelNumbers[channel]; 
  Serial.printf("Change channel to \"%s\" (channel number %d)\r\n", channel.c_str(), tvChannel);
  return true;
}
```

#### `onChangeChannelNumber(const String& deviceId, int channelNumber, String& channelName)`
Handles `changeChannel` requests by channel number. Updates `tvChannel` and returns the corresponding `channelName`.
```cpp
bool onChangeChannelNumber(const String& deviceId, int channelNumber, String& channelName) {
  tvChannel = channelNumber; 
  if (tvChannel < 0) tvChannel = 0;
  if (tvChannel > MAX_CHANNELS-1) tvChannel = MAX_CHANNELS-1;
  channelName = channelNames[tvChannel]; 
  Serial.printf("Change to channel to %d (channel name \"%s\")\r\n", tvChannel, channelName.c_str());
  return true;
}
```

#### `onMediaControl(const String &deviceId, String &control)`
Handles `mediaControl` commands (e.g., Play, Pause, Stop). Logs the control action.
```cpp
bool onMediaControl(const String &deviceId, String &control) {
  Serial.printf("MediaControl: %s\r\n", control.c_str());
  // Implement your media control logic here
  return true;
}
```

#### `onMute(const String &deviceId, bool &mute)`
Handles `setMute` commands. Updates `tvMuted`.
```cpp
bool onMute(const String &deviceId, bool &mute) {
  Serial.printf("TV volume is %s\r\n", mute?"muted":"unmuted");
  tvMuted = mute; 
  return true;
}
```

#### `onPowerState(const String &deviceId, bool &state)`
Handles `setPowerState` commands (on/off). Updates `tvPowerState`.
```cpp
bool onPowerState(const String &deviceId, bool &state) {
  Serial.printf("TV turned %s\r\n", state?"on":"off");
  tvPowerState = state; 
  return true; 
}
```

#### `onSelectInput(const String &deviceId, String &input)`
Handles `selectInput` commands. Logs the selected input.
```cpp
bool onSelectInput(const String &deviceId, String &input) {
  Serial.printf("Input changed to %s\r\n", input.c_str());
  // Implement your input switching logic here
  return true;
}
```

#### `onSetVolume(const String &deviceId, int &volume)`
Handles `setVolume` commands. Updates `tvVolume`.
```cpp
bool onSetVolume(const String &deviceId, int &volume) {
  Serial.printf("Volume set to:  %i\r\n", volume);
  tvVolume = volume; 
  return true;
}
```

#### `onSkipChannels(const String &deviceId, const int channelCount, String &channelName)`
Handles `skipChannels` commands. Adjusts `tvChannel` and returns the new `channelName`.
```cpp
bool onSkipChannels(const String &deviceId, const int channelCount, String &channelName) {
  tvChannel += channelCount; 
  if (tvChannel < 0) tvChannel = 0;
  if (tvChannel > MAX_CHANNELS-1) tvChannel = MAX_CHANNELS-1;
  channelName = String(channelNames[tvChannel]); 
  Serial.printf("Skip channel: %i (number: %i / name: \"%s\")\r\n", channelCount, tvChannel, channelName.c_str());
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
    delay(250);
  }
  IPAddress localIP = WiFi.localIP();
  Serial.printf("connected!\r\n[WiFi]: IP-Address is %d.%d.%d.%d\r\n", localIP[0], localIP[1], localIP[2], localIP[3]);
}
```

### `setupSinricPro()`
Initializes the SinricPro library and registers all the necessary callbacks for the TV device.
```cpp
void setupSinricPro() {
  SinricProTV& myTV = SinricPro[TV_ID];
  myTV.onAdjustVolume(onAdjustVolume);
  myTV.onChangeChannel(onChangeChannel);
  myTV.onChangeChannelNumber(onChangeChannelNumber);
  myTV.onMediaControl(onMediaControl);
  myTV.onMute(onMute);
  myTV.onPowerState(onPowerState);
  myTV.onSelectInput(onSelectInput);
  myTV.onSetVolume(onSetVolume);
  myTV.onSkipChannels(onSkipChannels);

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
  Serial.printf("%d channels configured\r\n", MAX_CHANNELS);
  setupWiFi();
  setupSinricPro();
  setupChannelNumbers();
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
*   `SinricProTV.h`
*   `<map>`

## Tags
`SinricPro`, `Example`, `TV`, `Smart Home`, `PowerState`, `Volume`, `Mute`, `Media Control`, `Input`, `Channel`, `ESP8266`, `ESP32`
