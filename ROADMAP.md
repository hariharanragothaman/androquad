# Androquad Roadmap

This document outlines the planned evolution of Androquad from a legacy Arduino-based flight controller to a modern ROS 2-based multi-rotor platform.

---

## Phase 0: Stabilize Legacy Codebase (Current)

**Goal**: Clean up and document the existing Arduino/AeroQuad code.

- [x] Fix typos and bugs in `androquad.ino` and `UserConfiguration.h`
- [x] Create comprehensive README with architecture documentation
- [x] Update `.gitignore` for Arduino projects
- [ ] Vendor or document the AeroQuad library dependency (currently external)
- [ ] Verify the sketch compiles against the AeroQuad library
- [ ] Add wiring diagrams and pin mapping documentation
- [ ] Document the current PID tuning values and control parameters

---

## Phase 1: ROS 2 Foundation

**Goal**: Set up the ROS 2 project structure and establish the basic framework.

- [ ] Create a ROS 2 workspace (`ros2_ws/`) alongside the legacy code
- [ ] Set up a ROS 2 package: `androquad_bringup` (launch files, configs)
- [ ] Set up a ROS 2 package: `androquad_description` (URDF/xacro model of the multirotor)
- [ ] Set up a ROS 2 package: `androquad_msgs` (custom message/service definitions)
- [ ] Define the target ROS 2 distribution (Humble / Iron / Jazzy)
- [ ] Set up CI with colcon build + linting (ament_cmake or ament_python)
- [ ] Choose a simulation environment (Gazebo Harmonic / Ignition)

---

## Phase 2: Simulation Environment

**Goal**: Build a simulation of the hexacopter in Gazebo before working with real hardware.

- [ ] Create URDF/SDF model of the Hex-Y6 airframe
- [ ] Integrate motor plugins for thrust simulation
- [ ] Add IMU, magnetometer, barometer sensor plugins
- [ ] Configure the Gazebo world with ground plane and obstacles
- [ ] Validate basic takeoff/hover/land in simulation
- [ ] Set up `ros2 launch` files for simulation bringup

---

## Phase 3: Core Flight Stack (ROS 2 Nodes)

**Goal**: Reimplement the flight control pipeline as modular ROS 2 nodes.

### Sensor Nodes
- [ ] `imu_driver_node` - Gyroscope + accelerometer data publisher (`sensor_msgs/Imu`)
- [ ] `mag_driver_node` - Magnetometer data publisher (`sensor_msgs/MagneticField`)
- [ ] `baro_driver_node` - Barometric pressure/altitude publisher
- [ ] `battery_monitor_node` - Battery voltage/current publisher (`sensor_msgs/BatteryState`)

### State Estimation
- [ ] `attitude_estimator_node` - AHRS / kinematics (replaces `Kinematics_ARG.h`)
  - Implement complementary filter or EKF
  - Publish `geometry_msgs/QuaternionStamped` or full `nav_msgs/Odometry`
- [ ] `altitude_estimator_node` - Fuse barometer + accelerometer for altitude
- [ ] `heading_estimator_node` - Magnetometer fusion for heading hold

### Control Nodes
- [ ] `pid_controller_node` - Attitude PID controller
  - Subscribe to attitude setpoints and estimated state
  - Publish motor commands
  - Support dynamic reconfigure for PID tuning (`rqt_reconfigure`)
- [ ] `altitude_controller_node` - Altitude hold PID
- [ ] `flight_command_node` - Translate pilot stick inputs to attitude/thrust setpoints
- [ ] `motor_mixer_node` - Mix attitude + thrust commands to individual motor PWM values
  - Support configurable motor layouts (quad/hex/octo)

### Actuator Nodes
- [ ] `motor_driver_node` - ESC/PWM output to motors
  - Abstract hardware interface for different ESC protocols (PWM, DShot, etc.)

### Interface Nodes
- [ ] `rc_receiver_node` - Read PPM/SBUS receiver input, publish `sensor_msgs/Joy`
- [ ] `camera_stabilizer_node` - Gimbal servo control based on attitude
- [ ] `osd_node` - On-screen display data publisher (optional)

---

## Phase 4: Navigation & Autonomy

**Goal**: Add autonomous capabilities using ROS 2 Nav2 concepts adapted for aerial vehicles.

- [ ] `gps_driver_node` - GPS data publisher (`sensor_msgs/NavSatFix`)
- [ ] `position_estimator_node` - Fuse GPS + IMU for global position (EKF via `robot_localization`)
- [ ] `waypoint_follower_node` - Accept and execute waypoint missions
- [ ] `geofence_node` - Safety boundary enforcement
- [ ] `auto_landing_node` - Automated landing using barometer + range finder
- [ ] `return_to_home_node` - RTH on failsafe or command
- [ ] Integrate with PX4 or ArduPilot via MAVROS2 / micro-XRCE-DDS (evaluate vs. native stack)

---

## Phase 5: Hardware Modernization

**Goal**: Move from Arduino Mega to a modern flight controller with ROS 2 support.

### Hardware Candidates
- [ ] Evaluate STM32-based boards (e.g., Pixhawk 6X, custom STM32H7)
- [ ] Evaluate companion computer options (Raspberry Pi 5, Jetson Orin Nano)
- [ ] Design split architecture: MCU (real-time control) + SBC (ROS 2, perception)

### Communication
- [ ] Implement micro-ROS on the MCU for real-time sensor/actuator control
- [ ] Use DDS (Fast DDS / Cyclone DDS) for inter-node communication
- [ ] Set up serial/USB bridge between MCU and companion computer

### Sensors
- [ ] Upgrade IMU (ICM-42688-P or BMI088)
- [ ] Upgrade magnetometer (QMC5883L or RM3100)
- [ ] Upgrade barometer (MS5611 or BMP390)
- [ ] Add optical flow sensor for indoor positioning
- [ ] Add LiDAR/ToF range finder for precise altitude hold

---

## Phase 6: Perception & Intelligence

**Goal**: Add perception capabilities for advanced autonomy.

- [ ] Camera integration (monocular / stereo / depth)
- [ ] Visual odometry / SLAM (ORB-SLAM3, RTAB-Map)
- [ ] Object detection and tracking
- [ ] Obstacle avoidance (local planner)
- [ ] Payload detection and tracking (swing-free trajectory for suspended loads)

---

## Phase 7: Fleet & Operations

**Goal**: Scale from single vehicle to multi-vehicle operations.

- [ ] Multi-robot coordination framework
- [ ] Ground control station integration (QGroundControl / custom ROS 2 GUI)
- [ ] Telemetry and logging infrastructure (ROS 2 bag recording, Foxglove)
- [ ] Mission planning interface
- [ ] Over-the-air (OTA) firmware updates

---

## Technology Stack (Target)

| Layer              | Technology                                          |
|--------------------|-----------------------------------------------------|
| **OS**             | Ubuntu 22.04+ / Ubuntu 24.04                        |
| **Middleware**     | ROS 2 (Humble/Jazzy), micro-ROS                     |
| **Build**          | colcon, CMake, ament                                 |
| **Simulation**     | Gazebo Harmonic                                      |
| **Visualization**  | RViz2, Foxglove Studio                               |
| **State Est.**     | robot_localization (EKF/UKF)                         |
| **Control**        | Custom PID nodes, ros2_control                       |
| **Perception**     | OpenCV, PCL, ORB-SLAM3                               |
| **Hardware MCU**   | STM32H7 + micro-ROS                                  |
| **Companion SBC**  | Raspberry Pi 5 / Jetson Orin Nano                    |
| **Communication**  | MAVLink 2 / DDS / Serial                             |
| **CI/CD**          | GitHub Actions                                       |

---

## Key Design Principles

1. **Modularity** - Each function is a standalone ROS 2 node with well-defined interfaces
2. **Simulation-first** - All features are validated in Gazebo before hardware testing
3. **Safety** - Failsafe at every layer (RC loss, battery, geofence, watchdog)
4. **Incremental migration** - Legacy Arduino code continues to fly while ROS 2 stack matures
5. **Hardware abstraction** - Control algorithms are decoupled from specific sensors/actuators
6. **Observability** - Every state is published as a ROS 2 topic for logging and debugging
