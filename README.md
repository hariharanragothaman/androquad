# Androquad

[![Platform](https://img.shields.io/badge/Platform-Arduino%20Mega%202560-blue?logo=arduino)](https://www.arduino.cc/)
[![Language](https://img.shields.io/badge/Language-C%2B%2B-00599C?logo=cplusplus)](https://isocpp.org/)
[![Flight Config](https://img.shields.io/badge/Config-Hex--Y6-orange)]()
[![ROS 2](https://img.shields.io/badge/ROS%202-Planned-brightgreen?logo=ros)](https://docs.ros.org/)
[![License](https://img.shields.io/badge/License-Open%20Source-green)]()
[![GitHub](https://img.shields.io/github/stars/hariharanragothaman/androquad?style=social)](https://github.com/hariharanragothaman/androquad)
[![Last Commit](https://img.shields.io/github/last-commit/hariharanragothaman/androquad)](https://github.com/hariharanragothaman/androquad/commits)
[![Repo Size](https://img.shields.io/github/repo-size/hariharanragothaman/androquad)](https://github.com/hariharanragothaman/androquad)

An Arduino-based quadrotor/multirotor flight controller built on the [AeroQuad](https://github.com/AeroQuad/AeroQuad) flight software platform. Originally developed circa 2013, this project targets the Arduino Mega 2560 with AeroQuad Shield v2.1 in a Hex-Y6 configuration.

## Overview

Androquad addresses the challenge of agile, swing-free trajectory tracking for multi-rotor UAVs with suspended loads. The project focuses on:

- Modeling, designing routines, and state estimation for an industrial multi-rotor
- Self-protection and situational awareness
- PID-based flight control with multi-rate task scheduling (100Hz / 50Hz / 10Hz / 1Hz)
- Support for magnetometer heading hold and barometric altitude hold
- Camera stabilization
- Extensibility for both terrestrial and aerial robotic platforms

## Architecture

```
androquad/
├── androquad.ino          # Main Arduino sketch (~1200 lines)
│                          #   - Platform-specific hardware abstraction
│                          #   - Multi-rate control loop (100/50/10/1 Hz)
│                          #   - Sensor initialization and processing
│                          #   - Flight control pipeline
├── UserConfiguration.h    # Build-time configuration header
│                          #   - Hardware board selection
│                          #   - Flight configuration (quad/hex/octo)
│                          #   - Sensor and feature enables
│                          #   - Receiver and telemetry settings
├── .gitignore
└── README.md
```

### Control Loop Architecture

| Rate   | Tasks                                                        |
|--------|--------------------------------------------------------------|
| 100 Hz | Gyro/Accel evaluation, kinematics, altitude estimation, flight control, GPS update, camera stabilization |
| 50 Hz  | Pilot command reading, RSSI, range finder updates            |
| 10 Hz  | Magnetometer/heading (task 1), battery/telemetry (task 2), OSD/LED status (task 3) |
| 1 Hz   | MavLink heartbeat                                            |

## Current Configuration

| Parameter         | Value                                          |
|-------------------|------------------------------------------------|
| **Board**         | Arduino Mega 2560 + AeroQuad Shield v2.1       |
| **Flight Config** | Hex-Y6 (6 motors in Y6 layout)                 |
| **Gyroscope**     | ITG-3200 (9DOF, alternate address)             |
| **Accelerometer** | ADXL345 (9DOF)                                 |
| **Magnetometer**  | HMC5883L (SparkFun 9DOF)                       |
| **Barometer**     | BMP085                                         |
| **Receiver**      | PPM (SERIAL_SUM_PPM_2, 8 channels)             |
| **ESC Rate**      | 400 Hz                                         |
| **Features**      | Heading hold, barometric altitude hold, camera stabilization |

## Supported Hardware Platforms

- **AeroQuad v1 / v1 IDG** - Arduino Uno + AeroQuad Shield v1.7 and below
- **AeroQuad v1.8** - Arduino Uno + AeroQuad Shield v1.8/1.9
- **AeroQuad Mini** - Arduino Pro Mini + Mini Shield v1.0
- **AeroQuad Mega v1/v2/v2.1** - Arduino Mega + AeroQuad Shield variants
- **AeroQuad Wii / Mega Wii** - Arduino with Wii sensor boards
- **ArduCopter (APM)** - ArduPilot Mega with Oilpan
- **CHR6DM variants** - CHR6DM as IMU/heading reference
- **AeroQuad STM32** - Baloo board (STM32-based)

## Supported Flight Configurations

| Config         | Motors | Layout      |
|----------------|--------|-------------|
| quadXConfig    | 4      | Quad X      |
| quadPlusConfig | 4      | Quad +      |
| quadY4Config   | 4      | Quad Y4     |
| triConfig      | 3      | Tricopter   |
| hexPlusConfig  | 6      | Hex +       |
| hexXConfig     | 6      | Hex X       |
| **hexY6Config**| **6**  | **Hex Y6** (currently active) |
| octoX8Config   | 8      | Octo X8     |
| octoXConfig    | 8      | Octo X      |
| octoPlusConfig | 8      | Octo +      |

## Dependencies

This sketch requires the **AeroQuad libraries** to be installed in your Arduino libraries path. These are **not included** in this repository. Key dependencies:

- `GlobalDefined.h`, `AeroQuad.h`, `PID.h`, `AQMath.h`, `FourthOrderFilter.h`
- Gyroscope drivers: `Gyroscope_ITG3200.h`, `Gyroscope_IDG_IDZ500.h`, etc.
- Accelerometer drivers: `Accelerometer_ADXL345.h`, `Accelerometer_BMA180.h`, etc.
- Magnetometer drivers: `Magnetometer_HMC5843.h`, `Magnetometer_HMC5883L.h`
- Barometer drivers: `BarometricSensor_BMP085.h`
- Receiver drivers: `Receiver_MEGA.h`, `Receiver_PPM.h`, etc.
- Motor drivers: `Motors_PWM_Timer.h`, `Motors_PWM.h`, etc.
- Flight control processors: `FlightControlHexY6.h`, etc.

## Getting Started (Legacy Arduino Build)

1. Install the [Arduino IDE](https://www.arduino.cc/en/software)
2. Clone the [AeroQuad libraries](https://github.com/AeroQuad/AeroQuad) into your Arduino `libraries/` folder
3. Clone this repository
4. Edit `UserConfiguration.h` to match your hardware:
   - Select your board (`#define AeroQuadMega_v21`, etc.)
   - Select your flight configuration (`#define hexY6Config`, etc.)
   - Enable/disable sensors and features
5. Open `androquad.ino` in Arduino IDE
6. Select **Arduino Mega 2560** as the board
7. Compile and upload

## Project Status

This project was originally developed ~12 years ago on the AeroQuad platform. It is now being revived with plans to migrate to **ROS 2** for modern robotics integration. See [ROADMAP.md](ROADMAP.md) for the planned evolution.

## License

This project builds upon the open-source AeroQuad flight software. Please refer to the original AeroQuad project for licensing details.
