# Lock_with_feedback Example

## Overview
This example demonstrates how to build a smart lock with feedback capabilities using SinricPro. Unlike a simple lock, this setup not only allows remote locking/unlocking but also reports the actual physical state of the lock (locked/unlocked) back to the SinricPro server. This is crucial for ensuring that the reported state in your smart home app accurately reflects the real-world status of the lock.

## Key Concepts/Capabilities Demonstrated
*   **Device Type**: `SinricProLock`
*   **Capabilities Used**:
    *   [`LockController`]: For handling `setLockState` commands (lock/unlock) and sending `sendLockStateEvent` for feedback.
*   **Digital Input**: Reading the physical state of the lock from a sensor (e.g., limit switch, contact sensor).
*   **State Synchronization**: Ensuring the reported state matches the physical state of the device.
*   **SinricPro Integration**: Demonstrates how to initialize SinricPro, register device-specific callbacks, and maintain the connection.
*   **WiFi Connectivity**: Standard ESP-based WiFi connection setup.

## Hardware Requirements
*   An ESP8266 or ESP32 development board.
*   A smart lock mechanism that can be controlled by the microcontroller (e.g., a servo, solenoid, or motor).
*   A sensor (e.g., limit switch, contact sensor) providing feedback on the lock's physical state (locked/unlocked).

## Setup Instructions
1.  **Install Libraries**: Ensure you have the SinricPro library and its dependencies installed. Refer to the main SDK documentation for installation instructions.
2.  **Update Credentials**: Modify the following `#define` statements in the `Lock_with_feedback.ino` file:
    *   `WIFI_SSID`: Your Wi-Fi network SSID.
    *   `WIFI_PASS`: Your Wi-Fi network password.
    *   `APP_KEY`: Your SinricPro App Key (from the SinricPro portal).
    *   `APP_SECRET`: Your SinricPro App Secret (from the SinricPro portal).
    *   `LOCK_ID`: The Device ID of your Smart Lock, created in the SinricPro portal (ensure it's of type `SMARTLOCK`).
3.  **Pin Configuration**: Define the GPIO pins for `LOCK_PIN` (to control the lock) and `LOCK_STATE_PIN` (to read feedback). The example assumes `HIGH` on `LOCK_STATE_PIN` means locked and `LOW` means unlocked.
4.  **Baud Rate**: Adjust `BAUD_RATE` if needed for your serial monitor.
5.  **Wiring**: Connect your lock mechanism to `LOCK_PIN` and your feedback sensor to `LOCK_STATE_PIN` as per your hardware setup.
6.  **Upload Sketch**: Upload the `Lock_with_feedback.ino` sketch to your ESP8266/ESP32 board.
7.  **Monitor Serial**: Open the serial monitor to observe connection status and command/feedback logs (uncomment `#define ENABLE_DEBUG` for more verbose output).

## Code Walkthrough

### Defines and Pin Assignments
```cpp
#define WIFI_SSID         "YOUR_WIFI_SSID"    
#define WIFI_PASS         "YOUR_WIFI_PASSWORD"
#define APP_KEY           "YOUR_APP_KEY_HERE"      
#define APP_SECRET        "YOUR_APP_SECRET_HERE"   
#define LOCK_ID           "YOUR_DEVICE_ID_HERE"    
#define BAUD_RATE         115200                     

#if defined(ESP8266)
  #define LOCK_PIN          D1                       // PIN where the lock is connected to: HIGH = locked, LOW = unlocked
  #define LOCK_STATE_PIN    D2                       // PIN where the lock feedback is connected to (HIGH:locked, LOW:unlocked)
#elif defined(ESP32) || defined(ARDUINO_ARCH_RP2040)
  #define LOCK_PIN          16                       // PIN where the lock is connected to: HIGH = locked, LOW = unlocked
  #define LOCK_STATE_PIN    17                       // PIN where the lock feedback is connected to (HIGH:locked, LOW:unlocked)
#endif

bool lastLockState;
```
These define your Wi-Fi and SinricPro credentials, the smart lock device ID, serial baud rate, and the GPIO pins for controlling the lock and reading its state feedback. `lastLockState` tracks the last reported physical lock state.

### Callbacks
This function is invoked when a corresponding command is received from the SinricPro server.

#### `onLockState(String deviceId, bool &lockState)`
Handles `setLockState` commands from SinricPro. It prints the requested state and then controls the physical lock mechanism by writing the `lockState` to `LOCK_PIN`.
```cpp
bool onLockState(String deviceId, bool &lockState) {
  Serial.printf("Device %s is %s\r\n", deviceId.c_str(), lockState?"locked":"unlocked");
  digitalWrite(LOCK_PIN, lockState);  
  return true;
}
```

### `checkLockState()`
This function continuously monitors the physical state of the lock by reading `LOCK_STATE_PIN`. If a change in the physical state is detected (e.g., the lock is manually operated), it updates `lastLockState` and sends a `sendLockStateEvent` to the SinricPro server to synchronize the reported state.
```cpp
void checkLockState() {
  bool currentLockState = digitalRead(LOCK_STATE_PIN);                                    
  if (currentLockState == lastLockState) return;                                          
  Serial.printf("Lock has been %s manually\r\n", currentLockState?"locked":"unlocked");   
  lastLockState = currentLockState;                                                       
  SinricProLock &myLock = SinricPro[LOCK_ID];                                             
  bool success = myLock.sendLockStateEvent(currentLockState);                             
  if(!success) {
      Serial.printf("Something went wrong...could not send Event to server!\r\n");
  }
}
```

### `setupWiFi()`
Connects the ESP board to your specified Wi-Fi network.
```cpp
void setupWiFi() {
  Serial.printf("\r\n[Wifi]: Connecting");
  WiFi.begin(WIFI_SSID, WIFI_PASS);
  #if defined(ESP8266)
    WiFi.setSleepMode(WIFI_NONE_SLEEP); 
  #elif defined(ESP32)
    WiFi.setSleep(false); 
  #endif
  while (WiFi.status() != WL_CONNECTED) {
    Serial.printf(".");
    delay(250);
  }
  Serial.printf("connected!\r\n[WiFi]: IP-Address is %s\r\n", WiFi.localIP().toString().c_str());
}
```

### `setupSinricPro()`
Initializes the SinricPro library and registers the necessary callbacks for the Smart Lock device.
```cpp
void setupSinricPro() {
  SinricProLock &myLock = SinricPro[LOCK_ID];
  myLock.onLockState(onLockState);

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
  pinMode(LOCK_PIN, OUTPUT);
  pinMode(LOCK_STATE_PIN, INPUT);
  setupWiFi();
  setupSinricPro();
}

void loop() {
  SinricPro.handle();
  checkLockState();
}
```
The `loop()` function continuously calls `SinricPro.handle()` to process SinricPro events and `checkLockState()` to monitor and report the physical lock state.

## Dependencies
*   `Arduino.h`
*   `ESP8266WiFi.h` (for ESP8266) or `WiFi.h` (for ESP32/RP2040)
*   `SinricPro.h`
*   `SinricProLock.h`

## Tags
`SinricPro`, `Example`, `Lock`, `Smart Lock`, `Feedback`, `State Synchronization`, `Security`, `Smart Home`, `ESP8266`, `ESP32`
