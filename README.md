# KMUTT Autonomous Golf Cart Localization & Mapping System

ระบบแผนที่ 3 มิติและการระบุตำแหน่งสำหรับรถกอล์ฟไฟฟ้าอัตโนมัติ ภายในมหาวิทยาลัยเทคโนโลยีพระจอมเกล้าธนบุรี (KMUTT)

โปรเจกต์นี้พัฒนาขึ้นสำหรับงานวิจัยและพัฒนารถกอล์ฟไฟฟ้าอัตโนมัติ โดยใช้เทคนิค LiDAR-Inertial Odometry และ Localization บน ROS2 Humble เพื่อให้รถสามารถสร้างแผนที่ 3 มิติ คำนวณตำแหน่งของตัวเอง และวิ่งตามเส้นทางอัตโนมัติได้ภายในพื้นที่มหาวิทยาลัย

---

# System Overview

ระบบประกอบด้วย 2 ส่วนหลัก

1. 3D Mapping (LIO-SAM)
2. Localization + Path Following

อุปกรณ์หลักที่ใช้ภายในระบบ

* Ouster OS1 LiDAR
* Xsens MTi-680 IMU/GNSS
* NVIDIA Jetson AGX Orin
* ROS2 Humble
* Ubuntu 22.04

---

# Features

* Real-time 3D Mapping
* LiDAR + IMU Fusion
* GPS/RTK Support
* Point Cloud Localization
* NDT Scan Matching
* Autonomous Path Following
* Pure Pursuit Controller
* RViz Visualization
* ROS2 Topic Integration
* Waypoint Navigation
* MQTT Communication with Web Interface

---

# System Architecture

## Mapping System

LIO-SAM ถูกใช้สำหรับสร้างแผนที่ 3 มิติจากข้อมูล LiDAR และ IMU โดยใช้แนวคิด Factor Graph Optimization

Pipeline หลักของระบบประกอบด้วย

1. LiDAR Point Cloud Input
2. IMU Preintegration
3. Point Cloud Deskewing
4. Feature Extraction
5. Scan Matching
6. Factor Graph Optimization
7. Global Map Generation

ผลลัพธ์คือแผนที่ Point Cloud 3 มิติใน frame `map`

---

## Localization System

ระบบ Localization ใช้ Point Cloud ปัจจุบันของรถ เปรียบเทียบกับแผนที่อ้างอิงที่สร้างไว้

Input หลัก

* `/cloud`
* `/imu/data`
* `/map`
* `/odom`
* `/initialpose`

Output หลัก

* `/pcl_pose`
* `/path`

เทคนิคที่ใช้

* NDT Scan Matching
* IMU Orientation Compensation
* Initial Pose Estimation
* TF Transformation

---

# Path Following Controller

ระบบควบคุมการวิ่งใช้ Pure Pursuit Controller ที่พัฒนาเพิ่มเติมสำหรับรถกอล์ฟไฟฟ้าอัตโนมัติ

Features ภายใน Controller

* Adaptive Lookahead Distance
* Cross Track Error Correction
* Heading Error Compensation
* Curve Speed Reduction
* Steering Deadband Compensation
* Vehicle Trace Visualization
* Remaining Distance Estimation
* Goal Detection
* Safety Stop

ROS2 Topics

Subscribe:

* `/nav_path`
* `/desired_path`
* `/pcl_pose`
* `/imu/data`

Publish:

* `/cmd_vel`
* `/target_waypoint`
* `/vehicle_trace`
* `/remaining_distance`
* `/path_following_done`

---

# ROS2 Environment

Tested Environment

* Ubuntu 22.04
* ROS2 Humble
* Ouster OS1-128
* Xsens MTi-680

Dependencies

```bash
sudo apt install ros-humble-pcl-msgs
sudo apt install ros-humble-perception-pcl
sudo apt install ros-humble-tf2
sudo apt install ros-humble-rviz2
```

---

# Installation

Clone Workspace

```bash
cd ~/ros2_ws/src

git clone https://github.com/Pakgard007/Localize.git
```

Build Workspace

```bash
cd ~/ros2_ws
colcon build
source install/setup.bash
```

---

# Run Mapping

```bash
ros2 launch lio_sam run.launch.py
```

---

# Run Localization

```bash
ros2 launch lidar_localization_ros2 localization.launch.py
```

---

# Run Waypoint Follower

```bash
ros2 run waypoint_follower waypoint_follower_cmdvel_imu
```

---

# RViz Visualization

ระบบรองรับการแสดงผลผ่าน RViz2

Visualization ที่ใช้ภายในโปรเจกต์

* Global Map
* Point Cloud
* Vehicle Pose
* TF Tree
* Navigation Path
* Vehicle Trace
* Cross Track Error
* Target Waypoint

---

# MQTT + Web Interface

Frontend ถูกพัฒนาด้วย Next.js และเชื่อมต่อกับ ROS2 ผ่าน MQTT Bridge

Features

* Search Location
* Voice Command
* Goal Selection
* Live Vehicle Tracking
* Autonomous Navigation Command
* Cesium 3D Visualization
* OSM Building Search

Topics ที่ใช้

* `/ros/goal`
* `/ros/path`
* `/ros/target_name`
* `/ros/command`

---

# Research Objective

วัตถุประสงค์ของโปรเจกต์นี้คือ

* พัฒนารถกอล์ฟไฟฟ้าอัตโนมัติ
* สร้างระบบแผนที่ 3 มิติภายในมหาวิทยาลัย
* พัฒนาระบบ Localization แบบ Real-time
* พัฒนาระบบนำทางอัตโนมัติสำหรับพื้นที่มหาวิทยาลัย
* เชื่อมต่อระบบ AI Assistant กับ Autonomous Navigation

---

# Example Applications

* Autonomous Campus Vehicle
* Smart Transportation System
* Indoor/Outdoor Localization
* Autonomous Delivery Vehicle
* Autonomous Shuttle

---

# Important Notes

* ระบบต้องใช้ 9-axis IMU
* ต้องตั้งค่า TF Frame ให้ถูกต้อง
* LiDAR และ IMU ต้อง Sync เวลาให้ตรงกัน
* แนะนำให้ใช้ RTK GPS สำหรับพื้นที่กลางแจ้ง
* Point Cloud ต้องมีข้อมูล ring และ timestamp

---

# Known Issues

## Zigzag Motion

สาเหตุส่วนใหญ่เกิดจาก timestamp ของ LiDAR และ IMU ไม่ตรงกัน

## Incorrect Pose Estimation

อาจเกิดจากการตั้งค่า IMU Extrinsic ไม่ถูกต้อง

## Sparse Feature Area

พื้นที่โล่ง เช่น สนามหญ้า อาจมี Feature น้อย ทำให้ Localization คลาดเคลื่อน

---

# Future Improvements

* Dynamic Obstacle Avoidance
* Multi-Sensor Fusion
* AI-based Navigation
* Semantic Mapping
* Cloud-based Monitoring
* Autonomous Parking

---

# Authors

Phuriphat Chomchuenphrueksa

Control and Instrumentation Engineering
King Mongkut’s University of Technology Thonburi (KMUTT)

---

# Reference

This project is developed based on the following open-source projects:

* LIO-SAM
* LeGO-LOAM
* lidar_localization_ros2
* ROS2 Navigation Stack

---

# Demonstration

Autonomous Golf Cart Platform for KMUTT Campus Navigation

* 3D Mapping
* Localization
* Autonomous Path Following
* Voice Assistant Navigation
* Real-time Visualization

---

Original reference adapted from LIO-SAM README. fileciteturn0file0
