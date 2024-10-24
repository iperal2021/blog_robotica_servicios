---
layout: default
title: Rescue People
nav_exclude: false
---

The goal of this exercise is to implement the logic that allows a quadrotor to recognize the faces of lost people and save their locations in order to perform a subsequent rescue maneuver.

The best form to face the problem is to divide the problem in two diferent parts: Navigation and face recognition.

### Navigation

To move the drone the available functions ar `HAL.set_cmd_pos(x, y, z, az)`, `HAL.set_cmd_vel(vx, vy, vz, az)` and a mixed option. We get the coordinates of both the boat (initial point) and the center of the rescue zone.

* Boat:  **40º16’48.2” N, 3º49’03.5” W**
* Survivors: **40º16’47.23” N, 3º49’01.78” W**

But the drone doesn´t understand *GPS* location, so I have to change it to *UMT* locations. The transformation gave me this:

```python
esting_boat = 430492
northing_boat = 4459162

esting_people = 430532
northing_people = 4459132

x = esting_people - esting_boat
y = northing_people - northing_boat
```

With this (X,Y) values I can move the drone to the location. Once there, I need to perform a sweep or scan of the zone to find as many survivors as possible. To get a better control over the image I decided to crop it into a circle.

For the scan movement I used a function with a *FSM* like in the main loop. This "sub-state machine" alternate between three diferent moves: right, back and left.

![sweep_move]()

By testing the move I choose the values of the maximun and minimun x and y that contains the zone were the victims are.

### Face recognition

Once the scan movement is ready, I have to perfom the recognition of faces to get the number and position of the posible victims. For this goal, I will create a fuction that will constantly check if there's a face in the image, and save the drone coordinates when one is on the image.

The first try using the face cascade from openc vhas results like these ones:

**** faltan imagenes ****

Due to the circular crop of the image, somtimes the cascade detecs all bodys as faces, so i decided to crop the image in squere form, to avoid this problem, but now, I have to implement a solution that turn the image until the face of victims are detected. I used the most common way among the python users and blogs from the web:

```python
def rotate_img(image, angle):
  
    (h, w) = image.shape[:2]
    center = (w // 2, h // 2)
    
    rotation_matrix = cv2.getRotationMatrix2D(center, angle, 1.0)
    rotated_image = cv2.warpAffine(image, rotation_matrix, (w, h))
    
    return rotated_image
    
```

> This code can be seen in these webs: [OpenCV documentation](https://docs.opencv.org/4.x/da/d6e/tutorial_py_geometric_transformations.html), [stackoverflow](https://stackoverflow.com/questions/9041681/opencv-python-rotate-image-by-x-degrees-around-specific-point), [geekforgeeks](https://www.geeksforgeeks.org/python-opencv-cv2-rotate-method/), [educative](https://www.educative.io/answers/opencv-rotate-image), [Medium](https://medium.com/analytics-vidhya/rotating-images-with-opencv-and-imutils-99801cb4e03e) and much more

Now the detections look like this:

**** subir imagenes****

### Return to Start point

I want two reasons for the drone to return to the (0,0,0) coordinate:

* **The scan is complete:** this means that the drone fly all over the rescue zone
* **The battery is low:** I need to simulate a real drone wich have limited battery, so when a certain time has passed, the drone will return to charge.

