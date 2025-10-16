# Speaker Example

## Overview
This example demonstrates how to integrate a smart speaker device with SinricPro, providing extensive control over its functionalities. It covers power state, volume control (set and adjust), mute/unmute, media playback controls (Play, Pause, Stop, Next, Previous, FastForward, Rewind, StartOver), input selection, equalizer settings (set, adjust, reset bands), and operational modes. This is a comprehensive example for building a feature-rich smart speaker.

## Key Concepts/Capabilities Demonstrated
*   **Device Type**: `SinricProSpeaker`
*   **Capabilities Used**: 
    *   [`PowerStateController`]: For managing the speaker's on/off state.
    *   [`VolumeController`]: For setting and adjusting the speaker's volume.
    *   [`MuteController`]: For muting and unmuting the speaker.
    *   [`MediaController`]: For controlling media playback (Play, Pause, Stop, etc.).
    *   [`InputController`]: For selecting different audio input sources.
    *   [`EqualizerController`]: For setting, adjusting, and resetting equalizer bands (Bass, Midrange, Treble).
    *   [`ModeController`]: For setting various operational modes (e.g., MOVIE, MUSIC).
*   **SinricPro Integration**: Demonstrates how to initialize SinricPro, register numerous device-specific callbacks, and maintain the connection.
*   **WiFi Connectivity**: Standard ESP-based WiFi connection setup.

## Hardware Requirements
*   An ESP8266 or ESP32 development board.
*   (Implied) A speaker system that can be controlled by the microcontroller (e.g., via I2S audio output, IR commands, or serial commands to an audio module).

## Setup Instructions
1.  **Install Libraries**: Ensure you have the SinricPro library and its dependencies installed. Refer to the main SDK documentation for installation instructions.
2.  **Update Credentials**: Modify the following `#define` statements in the `Speaker.ino` file:
    *   `WIFI_SSID`: Your Wi-Fi network SSID.
    *   `WIFI_PASS`: Your Wi-Fi network password.
    *   `APP_KEY`: Your SinricPro App Key (from the SinricPro portal).
    *   `APP_SECRET`: Your SinricPro App Secret (from the SinricPro portal).
    *   `SPEAKER_ID`: The Device ID of your Speaker, created in the SinricPro portal (ensure it's of type `SPEAKER`).
3.  **Baud Rate**: Adjust `BAUD_RATE` if needed for your serial monitor.
4.  **Upload Sketch**: Upload the `Speaker.ino` sketch to your ESP8266/ESP32 board.
5.  **Monitor Serial**: Open the serial monitor to observe connection status and command logs (uncomment `#define ENABLE_DEBUG` for more verbose output).

## Code Walkthrough

### Defines and Global State
```cpp
#define WIFI_SSID         "YOUR-WIFI-SSID"    
#define WIFI_PASS         "YOUR-WIFI-PASSWORD" 
#define APP_KEY           "YOUR-APP-KEY"      
#define APP_SECRET        "YOUR-APP-SECRET"   
#define SPEAKER_ID        "YOUR-DEVICE-ID"    
#define BAUD_RATE         115200

#define BANDS_INDEX_BASS      0
#define BANDS_INDEX_MIDRANGE  1
#define BANDS_INDEX_TREBBLE   2

enum speakerModes {
  mode_movie,
  mode_music,
  mode_night,
  mode_sport,
  mode_tv
};

struct {
  bool power;
  unsigned int volume;
  bool muted;
  speakerModes mode;
  int bands[3] = {0,0,0};
} speakerState;
```
These define your Wi-Fi and SinricPro credentials, the speaker device ID, serial baud rate, and constants for equalizer band indices. The `speakerModes` enum defines various operational modes. The `speakerState` struct holds the current state of the speaker, including power, volume, mute status, current mode, and equalizer band levels.

### Callbacks
These functions are invoked when a corresponding command is received from the SinricPro server.

#### `onPowerState(const String &deviceId, bool &state)`
Handles `setPowerState` commands (on/off). Updates `speakerState.power`.
```cpp
bool onPowerState(const String &deviceId, bool &state) {
  Serial.printf("Speaker turned %s\r\n", state?"on":"off");
  speakerState.power = state; 
  return true; 
}
```

#### `onSetVolume(const String &deviceId, int &volume)`
Handles `setVolume` commands. Updates `speakerState.volume`.
```cpp
bool onSetVolume(const String &deviceId, int &volume) {
  Serial.printf("Volume set to:  %i\r\n", volume);
  speakerState.volume = volume; 
  return true;
}
```

#### `onAdjustVolume(const String &deviceId, int &volumeDelta, bool volumeDefault)`
Handles `adjustVolume` commands. Adjusts `speakerState.volume` and returns the new absolute volume.
```cpp
bool onAdjustVolume(const String &deviceId, int &volumeDelta, bool volumeDefault) {
  speakerState.volume += volumeDelta;  
  Serial.printf("Volume changed about %i to %i\r\n", volumeDelta, speakerState.volume);
  volumeDelta = speakerState.volume; 
  return true;
}
```

#### `onMute(const String &deviceId, bool &mute)`
Handles `setMute` commands. Updates `speakerState.muted`.
```cpp
bool onMute(const String &deviceId, bool &mute) {
  Serial.printf("Speaker is %s\r\n", mute?"muted":"unmuted");
  speakerState.muted = mute; 
  return true;
}
```

#### `onMediaControl(const String &deviceId, String &control)`
Handles `mediaControl` commands (e.g., Play, Pause, Stop). Logs the received control action.
```cpp
bool onMediaControl(const String &deviceId, String &control) {
  Serial.printf("MediaControl: %s\r\n", control.c_str());
  // Implement your media control logic here
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

#### `onSetMode(const String &deviceId, String &mode)`
Handles `setMode` commands. Updates `speakerState.mode` based on the received mode string.
```cpp
bool onSetMode(const String &deviceId, String &mode) {
  Serial.printf("Speaker mode set to %s\r\n", mode.c_str());
  // Implement your mode setting logic here
  return true;
}
```

#### `onSetBands(const String& deviceId, const String &bands, int &level)`
Handles `setBands` commands. Updates the specified equalizer band's level.
```cpp
bool onSetBands(const String& deviceId, const String &bands, int &level) {
  Serial.printf("Device %s bands %s set to %d\r\n", deviceId.c_str(), bands.c_str(), level);
  // Implement your equalizer band setting logic here
  return true;
}
```

#### `onAdjustBands(const String &deviceId, const String &bands, int &levelDelta)`
Handles `adjustBands` commands. Adjusts the specified equalizer band's level and returns the new absolute level.
```cpp
bool onAdjustBands(const String &deviceId, const String &bands, int &levelDelta) {
  // Implement your equalizer band adjustment logic here
  return true; 
}  
```

#### `onResetBands(const String &deviceId, const String &bands, int &level)`
Handles `resetBands` commands. Resets the specified equalizer band's level to 0.
```cpp
bool onResetBands(const String &deviceId, const String &bands, int &level) {
  // Implement your equalizer band reset logic here
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
Initializes the SinricPro library and registers all the necessary callbacks for the Speaker device.
```cpp
void setupSinricPro() {
  SinricProSpeaker& speaker = SinricPro[SPEAKER_ID];
  speaker.onPowerState(onPowerState);
  speaker.onSetVolume(onSetVolume);
  speaker.onAdjustVolume(onAdjustVolume);
  speaker.onMute(onMute);
  speaker.onSetBands(onSetBands);
  speaker.onAdjustBands(onAdjustBands);
  speaker.onResetBands(onResetBands);
  speaker.onSetMode(onSetMode);
  speaker.onMediaControl(onMediaControl);
  speaker.onSelectInput(onSelectInput);

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
*   `SinricProSpeaker.h`

## Tags
`SinricPro`, `Example`, `Speaker`, `Smart Home`, `Audio`, `Volume`, `Mute`, `Media Control`, `Input`, `Equalizer`, `Mode`, `ESP8266`, `ESP32`
