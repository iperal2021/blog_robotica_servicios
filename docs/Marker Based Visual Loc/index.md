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

For last I multiply both matrix to get as result the aproximated coordinates of the robot.

### NAVIGATION

I choose a very simple form of navigatión wich chooses between a negative or positive angular speed to turn around randomly untill a new tag is detected.


### VIDEO