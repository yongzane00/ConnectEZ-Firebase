# ConnectEZ — Sensor-Logging Firmware (ESP32-S3 → Firebase)

ESP32-S3 firmware that reads an **MPU6050** accelerometer/gyroscope and a **MAX30102** heart-rate sensor, runs on-device **fall detection**, and logs incidents to **Google Firebase Realtime Database**. This is the sensor-logging component of **ConnectEZ**, an affordable AAC (Augmentative & Alternative Communication) device for aphasia patients — the accelerometer drives fall detection and the pulse sensor enables health monitoring, with a buzzer + button for local alerting.

> Medical Device Design & Biomedical Project Design & Management · team of 8

## Features

- Real-time fall detection from MPU6050 accelerometer + gyroscope data
- Heart-rate sensing via MAX30102
- Automatic logging of fall incidents (full motion data + timestamp) to Firebase RTDB
- Local buzzer alert with a button to acknowledge / silence
- Firebase Authentication (email/password) so each device writes under its own user UID

## Hardware & wiring

| Component | Connection | ESP32-S3 GPIO |
|-----------|------------|---------------|
| MPU6050 (accelerometer/gyro) | I²C SDA / SCL | 8 / 9 |
| MAX30102 (heart rate) | I²C SDA / SCL | 10 / 11 |
| Buzzer | signal | 12 |
| Button (ezButton) | signal | 5 |

Board: **ESP32-S3-DevKitC-1** (change `board` in `platformio.ini` for other variants).

## Tech stack

C++ · ESP32-S3 · Arduino framework · PlatformIO · Firebase Realtime Database · I²C

## How it works

On boot, `setup()` initialises both I²C sensors, connects to WiFi, and authenticates with Firebase. In `loop()`, the MPU6050 is polled and `fallDetection()` is evaluated; on a detected fall the firmware writes a full motion record to Firebase and triggers the buzzer (the button can acknowledge it). The MAX30102 heart rate is checked each cycle.

## Data schema (Firebase RTDB)

Records are written to `/UsersData/{uid}/readings/{timestamp}`:

```
x_axis, y_axis, z_axis            # acceleration
x_gyro, y_gyro, z_gyro            # angular velocity
total_acceleration                # magnitude
total_angular_velocity
timestamp
"Possible fall detected"          # fall flag
```

## Author

Yong Zane · zaneyong00@gmail.com

## License

See [LICENSE](LICENSE).
