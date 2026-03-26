# 3D Orientation Tracking and Panorama Reconstruction

**Course:** ECE 276A: Sensing & Estimation in Robotics  
**Institution:** University of California San Diego  
**Author:** Nathan Van Utrecht 

---

## ⚠️ Academic Integrity Notice
**The source code for this implementation is hidden in this public repository.** This is to comply with the strict academic integrity and collaboration policies of the course, which strictly forbid copying code. If you are a recruiter or researcher and would like to view the code, please contact me directly.

---

## Project Overview
This project presents a robust framework for 3D orientation tracking and panoramic image reconstruction using raw data from an Inertial Measurement Unit (IMU) and an RGB camera.  

The primary challenge in estimating orientation from raw IMU data is that integrating angular velocity (gyroscope data) is highly prone to drift over time, while linear acceleration (accelerometer data) is noisy and only provides a reference for the gravity vector. Fusing these sensor modalities is essential for maintaining a stable and accurate orientation estimate.

## Technical Approach

### 1. Orientation Tracking
To solve the drift and noise issues, the orientation tracking task is formulated as a constrained optimization problem. 
* **State Representation:** The body's orientation is represented using unit quaternions, $q_t \in \mathbb{H}_*$, to avoid the singularities associated with Euler angles.
* **Cost Function:** The optimization minimizes a cost function that penalizes deviations from two models:
  * A **kinematic motion model** derived from angular velocity integration.
  * An **observation model** that aligns the estimated gravity vector with accelerometer measurements.
* **Optimization:** The cost function is iteratively minimized using a projected gradient descent algorithm, which refines the trajectory while strictly enforcing the unit-norm constraint of the quaternions.

### 2. Panorama Stitching
Once the optimized orientation trajectory is estimated, the data is used to geometrically map a sequence of 2D RGB camera images onto a single static environment canvas. 
* **Temporal Synchronization:** Camera image timestamps are mapped to their nearest temporal neighbor in the IMU timestamp sequence.
* **Projection Pipeline:** The camera is modeled as a spherical sensor. Pixels are converted to unit spherical coordinates, transformed into Cartesian unit vectors in the camera frame, and then rotated into the global world frame using the optimized quaternion estimates.
* **Canvas Mapping:** The global vectors are mapped onto a 2048 x 1536 canvas using an equirectangular (cylindrical) projection. 

## Results

Experimental results across multiple datasets demonstrate that the proposed projected gradient descent optimization significantly reduces cumulative drift compared to raw angular velocity integration. This drift correction yields visually coherent panoramas, even in the presence of sensor noise and static initialization periods.

### Estimated Orientation vs. Ground Truth
[Plot of pitch, yaw, roll optimization](images/optimized_orientation.png)

### Reconstructed Panoramas
[Reconstructed panorama](images/panorama.png)
