# Vehicle Steering Control using LQG (LQR + Kalman Filter)
*Course project for ECE 171B: Linear Control System Theory at UC San Diego (Prof. Yuanyuan Shi)*

## Overview
This project addresses the classic problem of autonomous vehicle lateral control under uncertainty. It implements a Linear Quadratic Gaussian (LQG) controller to achieve robust lane following behavior.

In real-world autonomous driving, sensors (GPS/IMU) are noisy, and full state information is rarely available. This project bridges the gap by combining  Optimal Control (LQR) with Optimal Estimation (Kalman Filter) to achieve stable closed-loop performance using only noisy output measurements.

## Results

### 1. Baseline Performance (LQR)
The system was first tested under ideal, noise-free conditions to establish a performance benchmark.
![LQR Baseline]()
> **Observation:** The LQR controller achieves zero-error tracking of the sinusoidal reference path, verifying the model dynamics.

### 2. Robust Tracking under Noise (LQG)
Gaussian white noise was introduced to both process (wind/disturbance) and measurement (sensor noise) channels.
![LQG Tracking]()
> **Observation:** Despite noisy sensor data, the vehicle (Red) successfully tracks the reference path (Black). The LQG controller effectively filters out noise to prevent instability.

### 3. State Estimation Accuracy
The core of the LQG performance relies on the Kalman Filter's ability to estimate the true state.
![Kalman Filter]()
> **Observation:** The Estimated State (Dashed) overlaps closely with the Ground Truth (Solid), proving that the Kalman Filter is correctly tuned to recover the true vehicle dynamics from noisy outputs.

## Technical Approach

### 1. Kinematic Modeling
We utilized a linearized **Kinematic Bicycle Model** for lateral vehicle dynamics.
* **State Vector ($x$):** $[y, \theta]^T$ (Lateral position, Heading angle).
* **Control Input ($u$):** Steering angle rate.
* **Dynamics:** $\dot{x} = Ax + Bu$

### 2. Observability Analysis
Before designing the estimator, we verified the system's **observability** rank to ensure all internal states could be reconstructed from the available outputs.

### 3. Kalman Filter (State Estimation)
Designed an optimal state estimator to handle:
* **Process Noise ($w$):** Modeling system uncertainties and disturbances.
* **Measurement Noise ($v$):** Modeling sensor inaccuracies.
The filter minimizes the mean squared error of the estimated states $\hat{x}$.

### 4. LQR Controller (Optimal Control)
Computed the optimal state-feedback gain $K$ by minimizing the quadratic cost function:
$$J = \int_{0}^{\infty} (x^T Q x + u^T R u) dt$$
This balances the trade-off between **trajectory tracking error** and **control effort** (steering aggressiveness).

### 5. LQG Integration (Output Feedback)
The separation principle was applied to combine the estimator and controller into a unified **Output Feedback Control** law:
$$u = -K \hat{x}$$
