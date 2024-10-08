
# Autonomous Navigation using optical mouse sensor

We have endeavoured to make a bot which is capable of dead reckoning in indoor
settings. In this context, dead reckoning is the task of accurately estimating the position of the bot along the x and y axis with respect to the initial orientation (origin) using optical flow sensors (OFS) extracted from common gaming mouse. 

Uniqueness of our solution lies in providing highly accurate orientation using low cost OFS sensors with assisstance of Inertial Measurement Unit (IMU) and wheel encoder without the help of any external communication. Conventional options includes GPS and GNSS but are inaccurate for small distance traversing (< 5m) or  LiDAR and Time of Flight sensors (TOF) which are costly. 

The reason of using IMU and wheel encoder is that low cost OFS sensors create data with high error when not perfectly aligned with ground and relying solely on one type of sensor would simply be not enough for accurate results.

## Computing distance traversed using OFS

The two optical flow sensors (OFS) are indicated by points ( L \ and ( R ) in the schematic shown in Figure 1\. The point ( O ), which is the mid-point of ( L ) and ( R ), is designated as the center of the robot. Let the readings from the two sensors at ( L ) and ( R ) in the current step be (Δx<sub>L</sub>, Δy<sub>L</sub>)  and (Δx<sub>R</sub>, Δy<sub>R</sub> . These are indicated in Figure by arrows shown in blue.

<p align="center">
<img width = "300" height = "209" src = "https://raw.githubusercontent.com/Shiven-Patel-IIT/Autonomous-Navigation-using-Optical-Sensors/main/assets/optical_flow.png") >
</p>                            

Let the local coordinate system (LCS) of the robot be centered at the point ( O ). The displacement (Δx, Δy)  of the point ( O ) in the LCS is simply the average of individual components.

In order to compute ( Δθ), we note that the new positions of ( L ) and ( R ) in the LCS are ( L' = L + (Δx<sub>L</sub>, Δy_<sub>L</sub>)  and ( R' = R + (Δx<sub>R</sub>,  Δy<sub>R</sub>) \). Then \( Δθ ) may be obtained as

<p align="center"> Δθ= atan2(Δy<sub>R</sub> - Δy<sub>L</sub> , Δx<sub>R</sub> - Δx<sub>L</sub>) </p>


### Updating the position
Let the position of the robot at step ( i ) be denoted by  (x<sub>i</sub>, y<sub>i</sub>, Δ<sub>i</sub>)  and the displacement of the robot in step ( i ), as computed in Section 1, be (Δx<sub>i</sub>, Δy<sub>i</sub>, Δθ<sub>i</sub>) . Recall that the displacement thus obtained is in the LCS of the robot. In order to add it to the position  (x<sub>i</sub>, y<sub>i</sub>, θ<sub>i</sub>) , the vector (Δ<sub>i</sub>, Δy<sub>i</sub>) \) must be rotated by the angle \( θ<sub>i</sub> . This gives the updated position:
                        
<p align="center"> x<sub>i+1</sub> = x<sub>i</sub> + Δx<sub>i</sub>cos(θ<sub>i</sub>) - Δy<sub>i</sub>sin(θ<sub>i</sub>) </p>
<p align="center"> y<sub>i+1</sub> = y<sub>i</sub> + Δx<sub>i</sub>sin(θ<sub>i</sub>) + Δy<sub>i</sub>cos(θ<sub>i</sub>) </p>
<p align="center"> θ<sub>i+1</sub> = θ<sub>i</sub>+ Δθ<sub>i</sub> </p>


## IMU 
Our choice of IMU was ISM330DHCX which has an inbuilt accelerometer, gyroscope and magnetometer. The reason behind choosing this sensor was that high signal to noise ratio and availability of adequate libraries and options for customisation. The accelerometer gives acceleration in linear axes (X-Y-Z), the gyroscope gives angular velocities about X-Y-Z axes and magnetometer gives value of earth’s magnetic field in those axes.

### Caliberation of IMU
IMU requires some calibration as there might be some error due to misalignment of axes (orthogonality error) or some gibberish reading when the IMU is not moving ( zero error ). To remove such errors from the accelerometer and magnetometer, we have plotted alan-deviation plots of the values and calculated correction matrix and biases using Magneto software. Here is the picture of the plots.

![magneto_calib](https://github.com/user-attachments/assets/ff57cb6f-e66d-4eb5-bb3b-0b57cf741f1a) 

![Plot_3D](https://github.com/user-attachments/assets/18d404e4-b5ac-4399-aa04-4e916c2077d7) 

### Calculations
a simple double integration of linear acceleration will give results with a lot of drift error and values tend to blow up very quickly, to calculate the position and orientation of bot, we have tried to implement a Cubature Kalman Filter (CKF) (a type of Kalman filter with better capability to model nonlinearity and less computation cost).

### Kalman Filter
Kalman filter a state space model used to reduce the effect of noise on readings and estimate the state as close to ground truth. Here the state is [x, y, z, ˙x, ˙y, ˙z, θx, θy, θz ] to be calculated from readings of [¨x, ¨y, ¨z, ˙θx, ˙θy, ˙θz ], Here is the schematic version of kalman filter- 

  ![kalman-schematic](https://github.com/user-attachments/assets/7874e0d4-4339-4a1a-8b73-076d00ce0872) 


## Printed Circuit Board
Here is the design of our PCB which accomodates all the electrical connections including arduino, IMU, motor driver and motor
![PCBDesign](https://github.com/user-attachments/assets/600325bf-d160-4d07-abf2-c7197590886d) 

# How to run code
In order to ensure robot to run, power up arduino and Raspberry Pi using battery and load the file- "Getting_data_in_SI_imu_multi.ino" in arduino and file "main.py" in Raspberry Pi.


