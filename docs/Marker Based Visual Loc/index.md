---
layout: default
title: marked Loc
nav_exclude: false
---

## MARKED BASED VISUAL LOCALIZATION

The goal of this exercise is to estimate the position and orientation (pose) of a robot in a 2D space by detecting and analyzing visual markers, specifically AprilTags. This process involves using computer vision to identify the tags in the robot’s environment and mathematical methods to derive its relative pose with respect to the detected tags.

### EXERCISE STEPS

The exercise is not focused in navigation, so I used a simple random navigation function to move around the map.The main problem here is to get the correct *Transformation Matrix* for the calculations. I need to do these calculations:

* Get the TF from the center of coordinates to the tag (odom2tag)
* Get the TF from the tag to the robot (tag2camera)
* Multiply both matrix and get the TF from the center of coordinates to the robot (odom2camera)

### MATRIX

The matrix I need are created using the values of the coordinates I already have, as there are the coordinate sof the tags around the room, and the translation and rotation vector I get from the **PnP**(perspective-n-point) function.

The *tag2camera* matrix is made using `cv2.solvePnP` for what I needed the tags corners and coordinates that I got from the pyapriltags detectión. I used the camera calibration parameters from the documentation.

This vectors I obtain I have to transform them int a *4x4* matrix. This size is used to get the rotation and traslation in only one matrix. To do this I first created a identity matrix to then add the rotation in the first *3x3* rows-colums, and the traslationin to the forth colunm.

```python
    # SolvePnP for tag2camera
    success, rvec, tvec = cv2.solvePnP(tag_corners_3d, corners, camera_matrix, dist_coeffs, flags=cv2.SOLVEPNP_IPPE_SQUARE)
    if not success:
        return None, None

    # Rotation matrix
    rmat, _ = cv2.Rodrigues(rvec)
    tag2camera = np.eye(4)
    tag2camera[:3, :3] = rmat
    tag2camera[:3, 3] = tvec.flatten()
```

Then to get the correct matrix I need first to rotate it 90º in X and Z and inverse it for the final multiplication.

For the *odom2tag* matrix I used the known coordinates given by the config file in wich all the tags coordinates are. When I have a tag in the image, I search it's coordinates and orientation and use them in the matrix.

```python
    odom2tag = np.eye(4)
    print('odom2camera vacio', odom2tag)
    odom2tag[:3, 3] = [x, y, BALIZA_HEIGHT]
    odom2tag[:2, :2] = [[np.cos(yaw), -np.sin(yaw)],
                        [np.sin(yaw), np.cos(yaw)]]
```

For last I multiply both matrix to get as result the aproximated coordinates of the robot.To get the correct yaw value I use the same method as some classmates, calculating first the pitch and second the yaw:

```python
    pitch = math.atan2(
            -odom2cam[2][0], math.sqrt(odom2cam[0][0] * odom2cam[0][0] + odom2cam[1][0] * odom2cam[1][0])
        )
    yaw = math.atan2(odom2cam[1][0] / math.cos(pitch), odom2cam[0][0] / math.cos(pitch)) + math.pi/2
```

### NAVIGATION

I choose a very simple form of navigatión wich chooses a positive angular speed between 0.2 an 1 to turn around untill a new tag is detected.

### VIDEOS

The execution of the exercise show as that when the tag detected is too far the coordinates has noise wich also appear when the robot detecs again after the detection is lost.

[![IMAGE ALT TEXT](http://img.youtube.com/vi/jmBgod1rsAg/0.jpg)](https://youtu.be/jmBgod1rsAg "Auto Parking")

> Youtube URL if not displayed: [https://youtu.be/jmBgod1rsAg](https://youtu.be/jmBgod1rsAg)

Using a function wich calculates the mean between the current X and Y and the previus one I've been able to reduce the noise:

[![IMAGE ALT TEXT](http://img.youtube.com/vi/Rhf4Nc5tEMI/0.jpg)](https://youtu.be/Rhf4Nc5tEMI "Auto Parking")

> Youtube URL if not displayed: [https://youtu.be/jmBgod1rsAg](https://youtu.be/Rhf4Nc5tEMI)