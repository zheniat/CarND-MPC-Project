# CarND-Controls-MPC
Self-Driving Car Engineer Nanodegree Program

[//]: # (Image References)
[image1]: ./media/kinematic.png
[image2]: ./media/youtube.jpg

## Introduction
This project implements Model Predictive Control to drive a vehicle around the track using a simulator.

The project relies on third party libraries `Ipopt` and `CppAD`. `Ipopt` is a tool used to optimize the control inputs (throttle and steering angle) to find locally optimal values solving a non-linear problem. `CppAD` is a library used to perform automatic differentiation.

## Implementation
### Model

We use the kinematic vehicle model which takes vehicle `x` and `y` coordinates, angle `psi`, speed `v`, cross-track and orientation errors as input. The output of the model are optimal `steering` and `throttle` values.

![kinematic mode equations][image1]

The model applies constraints on actuator values to keep angle and throttle within a reasonable range.

The model uses a cost function to find locally optimal values. The cost function optimizes both errors (cross-track and orientation), limits speed to 70 mph, minimizes use of actuators and the value gap between sequential actuations to result in smooth driving.

### Timestep Length and Elapsed Duration

The timestep length was set to 10 (`N`) with elapsed duration between steps set to 0.1s (`dt`). A short time horizon was chosen since the road frequently curves, changing the environment. A small number of time steps ensured quicker computation by reducing the number of variables to optimize. Timestep set to 0.1s matched system's latency, simplifying calculations to compensate for the latency. Longer timesteps (25) and shorter durations (0.05) caused the vehicle to become quickly unstable.

### Model Predictive Control with Latency
The system has a 100ms latency for the vehicle to perform actuations. Not taking latency into account causes the vehicle to swerve off the road since the vehicle acts on outdated data. In order to address this the kinematic model was used to project the vehicle's state (x, y, angle, speed) 100ms into the future using the kinematic model. The future state was then used to find optimal actuator values.

### Polynomial Fitting and MPC Preprocessing

In order to simplify calculations the waypoints were transformed from the global coordinates to the vehicle coordinates. This transformation set `x`, `y` coordinates and vehicle angle to zero simplifying fitting of the polynomial.

## Simulation

The vehicle was able to successfully drive around the track at ~70 mph.

[![drive around the lap][image2]](https://www.youtube.com/watch?v=lX50i6LChsI)
