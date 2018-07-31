GyroscopeExplorer
=================

<img src="http://kircherelectronics.com.23.38-89-161.groveurl.com/wp-content/uploads/2017/12/Screenshot_20171228-164911.png" width="270">

 <a href="https://play.google.com/store/apps/details?id=com.kircherelectronics.com.gyroscopeexplorer"><img src="http://kircherelectronics.com.23.38-89-161.groveurl.com/wp-content/uploads/2017/12/en_badge_web_generic.png" width="270"></a>

# Introduction

Gyroscope Explorer provides a working open source code example and Android application that demonstrates how to use the gyroscope sensor for measuring the rotation of an Android device. While this example is implemented with Android/Java, the jist of the algorithm can be applied to almost any hardware/language combination to determine linear rotation.

# Backed by FSensor

![Alt text](http://kircherelectronics.com.23.38-89-161.groveurl.com/wp-content/uploads/2017/12/FSensor.png "FSensor")

The latest release of Gyroscope Explorer is now backed by [FSensor](https://github.com/KalebKE/FSensor). *If you are interested in implementing the sensor fusions, you want to go there.* FSensor (FusionSensor) is an Android library that (hopefully) removes some/most of the complexity of using Androids orientation sensors (Acceleration, Magnetic and Gyroscope). You can now just link FSensor to your project and get coding. No more having to wade through dense code to pick the parts you need.

## Overview of Features

Gyroscope Explorer contains Android classes that demonstrate how to use the Sensor.TYPE_GYROSCOPE and Sensor.TYPE_GYROSCOPE_UNCALIBRATED. This includes integrating the sensor outputs over time to describe the devices change in angles, initializing the rotation matrix,  concatenation of the new rotation matrix with the initial rotation matrix and providing an orientation for the concatenated rotation matrix. The Android developer documentation covers some of this information, but it is an incomplete example. Gyroscope Explorer provides an example that is fully implemented. Gyroscope Explorer provides the Earth frame orientation with the azimuth, pitch and roll and described in a clean graphical view.

### Caveat Emptor

Note that the gyroscope is subject to drift despite the fact that Sensor.TYPE_GYROSCOPE is supposed to compensate for drift. The gyroscope is also very sensitive to rapid rotations and external vibrations. 

Gyroscope Explorer also provides implementations of gyroscope sensor fusions that offer much more robust and reliable estimations of the device's rotation. The sensor fusions use the rotation sensor, magnetic sensor and gyroscope sensor to calculate rotation measurements that are not affected by rapid rotations or external vibrations.

Gyroscope Explorer Features:

* View the output of all of the sensors axes in real-time
* Log the output of all of the sensors axes to a .CSV file
* Mean filter for data smoothing
* Sensor fusions include three complimentary (Euler angle, rotation matrix and quaternion) and one Kalman (quaternion) filter.
* Visualize the tilt of the device
* Compare the performance of multiple devices

## Smoothing filters

Gyroscope Explorer implements the most common smoothing filter, a mean filters. The mean filter is designed to smooth the data points based on a time constant in units of seconds. The mean filter will average the samples that occur over a period defined by the time constant... the number of samples that are averaged is known as the filter window. The approach allows the filter window to be defined over a period of time, instead of a fixed number of samples. The mean filter is user configurable based on the time constant in units of seconds. The larger the time constant, the smoother the signal. However, latency also increases with the time constant. Because the filter coefficient is in the time domain, differences in sensor output frequencies have little effect on the performance of the filter. The smoothing filter should perform about the same across all devices regardless of the sensor frequency.

### Quaternions Complimentary Filter (ImuOCfQuaternion)

The complementary filter is a frequency domain filter. In its strictest sense, the definition of a complementary filter refers to the use of two or more transfer functions, which are mathematical complements of one another. Thus, if the data from one sensor is operated on by G(s), then the data from the other sensor is operated on by I-G(s), and the sum of the transfer functions is I, the identity matrix. In practice, it looks nearly identical to a low-pass filter, but uses two different sets of sensor measurements to produce what can be thought of as a weighted estimation. 

In most cases, the gyroscope is used to measure the devices orientation. However, the gyroscope tends to drift due to round off errors and other factors. Most gyroscopes work by measuring very small vibrations in the earth's rotation, which means they really do not like external vibrations. Because of drift and external vibrations, the gyroscope has to be compensated with a second estimation of the devices orientation, which comes from the rotation sensor and magnetic sensor. The rotation sensor provides the pitch and roll estimations while the magnetic sensor provides the azimuth. A complimentary filter is used to fuse the two orientations together. It takes the form of gyro[0] = alpha * gyro[0] + (1 - alpha) * accel/magnetic[0]. Alpha is defined as alpha = timeConstant / (timeConstant + dt) where the time constant is the length of signals the filter should act on and dt is the sample period (1/frequency) of the sensor.

Quaternions offer an angle-axis solution to rotations which do not suffer from many of the singularities, including gimbal lock, that you will find with rotation matrices. Quaternions can also be scaled and applied to a complimentary filter. The quaternion complimentary filter is probably the most elegant, robust and accurate of the filters, although it can also be the most difficult to implement.

### Quaternion Kalman Filter (ImuOKfQuaternion)

Kalman filtering, also known as linear quadratic estimation (LQE), is an algorithm that uses a series of measurements observed over time, containing noise (random variations) and other inaccuracies, and produces estimates of unknown variables that tend to be more precise than those based on a single measurement alone. More formally, the Kalman filter operates recursively on streams of noisy input data to produce a statistically optimal estimate of the underlying system state. Much like complimentary filters, Kalman filters require two sets of estimations, which we have from the gyroscope and rotation/magnetic senor. The Acceleration Explorer implementation of the Kalman filter relies on quaternions.

For more information on integrating the gyroscope to obtain a quaternion, rotation matrix or orientation, see [here](http://www.kircherelectronics.com/blog/index.php/11-android/sensors/15-android-gyroscope-basics).

Published under [Apache License, Version 2.0](http://www.apache.org/licenses/LICENSE-2.0)

