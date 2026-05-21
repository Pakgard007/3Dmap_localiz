<div align="center">

# 🚗 KMUTT Autonomous Golf Cart

## 3D Mapping & Localization System

**LiDAR-Inertial Mapping and Point Cloud Localization for an Autonomous Electric Golf Cart**

![ROS2](https://img.shields.io/badge/ROS2-Humble-blue)
![Ubuntu](https://img.shields.io/badge/Ubuntu-22.04-orange)
![LiDAR](https://img.shields.io/badge/LiDAR-Ouster%20OS1-green)
![IMU](https://img.shields.io/badge/IMU-Xsens%20MTi--680-purple)
![Status](https://img.shields.io/badge/Status-Research%20Project-success)

</div>

---

## 📌 Project Overview

This project focuses on the **3D Mapping** and **Localization** system for an autonomous electric golf cart developed for operation inside **King Mongkut’s University of Technology Thonburi (KMUTT)**.

The system uses **LiDAR**, **IMU**, and **GNSS/RTK** data to build a 3D point cloud map and estimate the vehicle position in real time. The generated map is used as the reference environment for localization and autonomous navigation.

---

## 🧭 Main System Modules

| Module           | Description                                                                       | Main Output       |
| ---------------- | --------------------------------------------------------------------------------- | ----------------- |
| **3D Mapping**   | Builds a 3D point cloud map using LiDAR and IMU data                              | Global `.pcd` map |
| **Localization** | Estimates the vehicle pose by matching current LiDAR scans with the pre-built map | `/pcl_pose`       |

---

# 🗺️ 1. 3D Mapping

## 1.1 Overview

The 3D Mapping module is based on **LIO-SAM**, a LiDAR-Inertial Odometry and Mapping framework. It fuses LiDAR point clouds and IMU measurements to estimate vehicle motion and generate a consistent 3D point cloud map.

The final map is used as the reference map for the localization system.

---

## 1.2 Sensor Inputs

| Sensor   | Model         | Purpose                                  |
| -------- | ------------- | ---------------------------------------- |
| LiDAR    | Ouster OS1    | Collect 3D point cloud data              |
| IMU      | Xsens MTi-680 | Support motion estimation and deskewing  |
| GNSS/RTK | Xsens MTi-680 | Reduce long-term drift in open-sky areas |

Related ROS2 topics:

```bash
/ouster/points
/ouster/imu
/imu/data
/gnss
/gnss_pose
```

---

## 1.3 Mapping Workflow

```text
LiDAR Point Cloud
        ↓
IMU Data Integration
        ↓
Point Cloud Deskewing
        ↓
Feature Extraction
        ↓
LiDAR Odometry
        ↓
Factor Graph Optimization
        ↓
Global 3D Point Cloud Map
```

The mapping process estimates the vehicle trajectory while continuously adding corrected point clouds into the global map frame.

---

## 1.4 Mapping Output

The main outputs from the 3D Mapping module are:

* 3D Point Cloud Map
* LiDAR Odometry
* Vehicle Trajectory
* Global Map Frame
* `.pcd` map file for localization

---

## 1.5 Save Map Command

Basic command:

```bash
ros2 service call /lio_sam/save_map lio_sam/srv/SaveMap
```

Command with resolution and destination path:

```bash
ros2 service call /lio_sam/save_map lio_sam/srv/SaveMap "{resolution: 0.2, destination: /home/user/Downloads/map}"
```

---

## 1.6 Important Notes for Mapping

> These points are important for stable and accurate mapping.

* LiDAR point cloud should include `ring` and `time` fields.
* IMU should be properly aligned with the LiDAR frame.
* LiDAR and IMU timestamps must be synchronized.
* Correct extrinsic calibration is required.
* Open areas with few features may reduce mapping accuracy.
* GNSS/RTK can help reduce drift in outdoor environments.

---

# 📍 2. Localization

## 2.1 Overview

The Localization module estimates the current position and orientation of the autonomous golf cart on the pre-built 3D map.

It compares the current LiDAR scan with the saved point cloud map using scan matching methods such as **NDT Scan Matching**. The estimated pose is published in the `map` frame and can be used by the path-following controller.

---

## 2.2 Localization Inputs

| Input               | Description                             |
| ------------------- | --------------------------------------- |
| Current Point Cloud | Real-time LiDAR scan from the vehicle   |
| Pre-built Map       | `.pcd` map generated from LIO-SAM       |
| IMU Data            | Supports orientation estimation         |
| Odometry            | Helps initialize scan matching          |
| Initial Pose        | Provides starting pose for localization |

Related ROS2 topics:

```bash
/cloud
/map
/imu
/odom
/initialpose
```

---

## 2.3 Localization Workflow

```text
Pre-built 3D Map
        ↓
Current LiDAR Scan
        ↓
Initial Pose / Odometry
        ↓
IMU Orientation Support
        ↓
NDT Scan Matching
        ↓
Vehicle Pose Estimation
        ↓
Publish /pcl_pose
```

---

## 2.4 Main Method

The localization system uses point cloud registration to align the current scan with the reference map.

Main techniques:

* NDT Scan Matching
* Point Cloud Registration
* IMU Orientation Compensation
* TF Transformation
* Map-based Pose Estimation

---

## 2.5 Localization Output

The main outputs from the Localization module are:

```bash
/pcl_pose
/path
```

Additional outputs:

* Current Vehicle Pose
* Vehicle Trajectory
* TF between `map`, `odom`, and `base_link`

---

## 2.6 Important Notes for Localization

> Localization accuracy depends on map quality, initial pose, and environmental features.

* The `.pcd` map must match the real environment.
* Initial pose should be close to the actual vehicle position.
* Large environmental changes may reduce localization accuracy.
* Open areas with sparse features can cause scan matching errors.
* Moving objects may temporarily disturb point cloud matching.

---

# ⚙️ System Environment

| Component      | Version / Model        |
| -------------- | ---------------------- |
| OS             | Ubuntu 22.04           |
| ROS            | ROS2 Humble            |
| LiDAR          | Ouster OS1             |
| IMU/GNSS       | Xsens MTi-680          |
| Computing Unit | NVIDIA Jetson AGX Orin |

---

# 🚀 Basic Commands

## Build Workspace

```bash
cd ~/ros2_ws
colcon build
source install/setup.bash
```

## Run 3D Mapping

```bash
ros2 launch lio_sam run.launch.py
```

## Run Localization

```bash
ros2 launch lidar_localization_ros2 localization.launch.py
```

---

# 🎯 Project Objective

The objective of this project is to develop a reliable 3D Mapping and Localization system for an autonomous electric golf cart. The system enables the vehicle to understand its surrounding environment, estimate its position in real time, and provide accurate pose information for autonomous navigation inside the KMUTT campus.

---

# 🧑‍💻 Author


Control and Instrumentation Engineering
King Mongkut’s University of Technology Thonburi (KMUTT)

---

# 📚 Reference

This project is adapted from LIO-SAM and related ROS2 LiDAR localization packages. fileciteturn0file0
