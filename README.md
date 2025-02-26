Download link :https://programming.engineering/product/lab-7robot-vision-ii-camera-calibration/

# LAB-7Robot-Vision-II-Camera-Calibration
LAB 7Robot Vision II: Camera Calibration
Important
Read the entire lab before starting and especially the “Grading” section so you are aware of all due dates and requirements associated with the lab. There will be only one online session for this lab, so it is important that you prepare for that one opportunity to work with your TA.

Scope
Camera Calibration – Obtain the relation between camera and world ref- erence frame

Robot-Camera Calibration – Obtain the mapping between the camera and robot coordinates

Image-Guided Manipulation – Plan and execute path for manipulation task

Objectives
This is the capstone lab of the semester and will integrate your work done in previous labs with python, ROS, and forward and inverse kinematics. It will also introduce you to some basic OpenCV techniques. In this lab you will:

Use OpenCV functions to distinguish the shape of the blocks and find the centroid and angle of them.

Develop transformation equations that relate pixels in the image to coor- dinates in the world frame

Figure 7.1: Central Projection Camera Model

Report the world frame coordinates (xw, yw) of the centroid of each block in the camera’s view.

Move the block to a predefined position in the robot’s work area.

Background
The Camera Model and Calibration Process
Robotic systems commonly rely on visual information from the surrounding to perceive the environment. The source of this information typically comes in the form of images depicting the scene of the camera views. Therefore, camera can be an extremely useful sensing device to provide a robotic system with control feedback and perception. Like many other sensors, there is usually a need to map the acquired signal (imagery) to measurement (spatial) through a calibration process based on a camera model. The central projection camera model as shown in Figure 7.1 is a representation of the image formation process relating points in world coordinates to their projection on a virtual image plane.

Camera calibration uses known image data of scene from different camera views to establish a mapping between the camera pose and the world reference. This mapping is usually in the form of a camera calibration matrix expressed as

M = C(R | t) (7.1)

where R and t are the 3-by-3 rotation matrix and 3-by-1 translation vector, respectively, from the camera’s viewpoint. Together they are known as the extrinsic matrix. Matrix C is a 3-by-3 matrix with intrinsic parameters including

its focal length (fx, fy)T , principal point (ic, jc)T expressed as

 fx a io 



Figure 7.2: Workflow for the task of image-guided robotic object sorting


Figure 7.3: Registering two positions to find image-robot relationship

Define relationship between the robot base frame and the pixel coor- dinate frame as illustrated by the red and blue axes, respectively.

Move robot to Region 1: pixel coordinates (x1, y1); register the robot coordinates as (x′ , y′ ).

1 1

Move robot to Region 2: pixel coordinates (x2, y2); register the robot coordinates as (x′ , y′ )

2 2

Obtain the ratio that map (x, y) to (x′, y′) as define below:

xratio = (x′ − x′ ) / (y2 − y1)

2 1 (7.4)

yratio = (y′ − y′ ) / (x2 − x1)

2 1



Figure 7.4: Image processing to classify and localize object in camera scene

Move to sort objects into specific pose in the sorted tray as shown in Figure 7.5

Debugging
This lab can be a bit tricky to work through as several parts require trial and error. Here are some suggestions:

Approach the lab sequentially and make sure you get good results at each step.

Comment out parts that you are not currently using (Don’t forget the uncomment them though…)

Report
This lab does not require a formal lab report – only the submission of your code. You should a submit a document to Blackboard containing all of the files that you edited to complete the lab. This will be submitted to Blackboard as in the past.

Demo
Your TA will require you to run your program twice, each time with different initial block positions and verify that your program can locate the blocks and place them in the correct location. You will also demonstrate that suction feedback is working.



Figure 7.5: Image processing to classify and localize object in camera scene

Grading
100 points, successful demonstration and submission of the code. (No code

= 0 points)

Appendix B

Notes on Camera Calibration and Transformations
Simplified Perspective Transform
To extract spatial information from camera images, we need to derive equations to compute an object’s coordinates in the world frame (x, y, z)w based on row and column pixel coordinates in the image (r, c)c. Instead of giving you these equations, this section will walk through how the transform works to allow you to derive them yourself.


Figure B.1: The view from the camera.

The image starts as seen in Figure B.1. We can see that all data is in pixels, so

the location of the object would be described by the rows and columns of the pixel at the centroid of the red block. We can further see that the image is not square to the table, but instead slightly rotated. We will ignore that for the time being.


Figure B.2: The view from the camera with the origin shifted to the principal point.

The first step is to translate the frame from the top left corner, to the center of the image also known as the principal point as seen in Figure B.2. As seen in Table B.1, the location of the principal point is in our original frame are called (Or, Oc). Based on this image, what are Or and Oc

Or =

Oc =

Side note: It is possible to develop the camera transformation without this translations, but shifting to the principle point is the standard method. It is recommended that you consult a more exhaustive text on computer vision to better understand why.

The next step is to move from pixel measurements to something we can measure in the real world such as meters. To do this, we need a relationship between pixel distance and physical distance on the table top. This value depends both on the physical properties of the camera and the distance between the camera and table top. The physical properties of the camera are encapsulated by focal length (fx and fy as seen in Table B.1). Because we do not know the focal length of the camera, we instead create a simple value β which encapsulates both pieces of information:

β = fx = fy .

zc zc

This is a simplification of this relationship. It assumes that pixels are square and that the lens does not distort the image at all. Because of this, you might find it is less accurate at the edges of the image, but it should work fine for our purposes. The units of β are pixels per meter.

We now have enough information to complete our first equation. This equation gives us the location of the block in meters, but with respect to the camera frame and not the world frame. These equations are often called the intrinsic equations because they rely on the intrinsic properties of the camera such as Or, Oc, fx and fy (i.e. properties that don’t change). These equations are essentially a transition from the camera sensor to the tabletop as seen in Figure B.3. Please complete the intrinsic equations using only β, Or, and Oc

xc(r) =

yc(c) =


Figure B.3: The camera frame projected onto the table top. This is the output of the intrinsic equations.

Now that we have the intrinsic equations and have transitioned to the table top, we can try to align this table top camera frame with the world frame at the corner of the aluminum plate. We start this by shifting the frame from its current position which sits where the principal point lines up on the table top as shown in Figure B.4. As seen in Table B.1, the relationship between these two frames is Ocw which is the origin of the world frame in terms of the camera frame. The key values here are Tx and Ty as Tz = 0.

Now that we have aligned the origins, it is time to straighten up the frame as shown in Figure B.5. We do this naturally by a rotation about the z-axis by the angle θ. We can see in Table B.1 that θ is defined for Rcw.

Now that we have translated and rotated our frame to align with the world frame, we can write the extrinsic equations. These are known as extrinsic



Figure B.4: The table top camera frame must be translated to align with the world frame.

equations because they are based on values outside of the camera and change depending on placement and orientation. Let’s write the extrinsic equations:



xw =

yw

Pay special attention to the signs of Tx and Ty!

We now have both the intrinsic and extrinsic equations and we can put them both together:

xw(r, c) =

yw(r, c) =

These are the equations that you will use in your code to perform the camera transformation. What about zw? Well, since we are working on the flat table top, zw is just the height of any object we wish to pick up. A value for zw is provide in the code.

Camera Calibration
After formulating the camera transformation it is natural to ask how the key values such as β, θ, Tx and Ty are found. This is through the process of camera calibration. Due to simulation limitations, this has already been done for this lab. This section will give a brief introduction to how this process works.

β – This value is found using a calibration stick. The stick has two target set 10cm apart (center to center). When the centroids of these targets are


Figure B.5: The table top camera frame must be rotated to align with the world frame.

Name Description

(r, c) (row,column) coordinates of image pixel

fx, fy ratio of focal length to physical width and length of a pixel (Or, Oc) (row,column) coordinates of principal point of image

(principal point of our image is center pixel) (xc, yc) (meters) coordinates of point in camera frame

Ocw = [TxTyTz]T origin of world frame expressed in camera frame

Rcw rotation expressing world frame w.r.t. camera frame.

Table B.1: Definitions of variables necessary for camera calibration.

found, their distance in pixels can be determined. It is then just a matter of relating the pixels to the fixed 10cm distance.

θ – The same calibration stick is used again. It is placed on the table top such that it carefully aligns with the y-axis of the world frame. When the centroids of the targets are found, a right triangle can be formed with the two points and the angle formed will be θ.

Tx and Ty – This time a single target is placed on the table top with a known position in the world frame. With this known positions, the location of the centroid (r,c) and β and θ, the only unknown in the camera transformation equations are Tx and Ty, which can easily be solved for.
