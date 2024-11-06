---
layout: default
title: Auto Parking
nav_exclude: false
---

## AUTO PARKING

The objective of this practice is to implement the logic of a navigation algorithm for an automated vehicle. The vehicle must find a parking space and park properly.
To implement a strong algorithm that could work in any type of street the steps I want to follow are these:

* Travel straigh in any street.
* Check if there is a parking with enough space for the car.
* Start the parking manoeuvre.
* Check if the orientation in the parking is the same as the one of the street (if the car is parallel to the road)

### Linear Movement

To get a good linear movement in any case of street I use a PD controller that use the right laser divided in two parts (from 0 to 90 and from 91 to 180). I get the average measurement of each part and compare them to get the error between. With the error I can use the PD to controll the angular velocity. I will use the same method of get the average measurement of a laser range to get the front and the back lasers and detect if there is a parking.

After time of adjust the PD and test, I decided to discard this idea because when I found the parking, the car starts to turn to the left without stop, making the car to go outside the road. The new way that I will use to get a straight linear movement in any road is using the `parse_laser_data()` function from the **Localiced Vacuum Cleaner** exercise because with it I can get *X,Y* coordinates from the laser.

```python
def parse_laser_data(laser_data):
    """ Parses the LaserData object and returns a tuple with two lists:
        1. List of  polar coordinates, with (distance, angle) tuples,
           where the angle is zero at the front of the robot and increases to the left.
        2. List of cartesian (x, y) coordinates, following the ref. system noted below.

        Note: The list of laser values MUST NOT BE EMPTY.
    """
    laser_polar = []  # Laser data in polar coordinates (dist, angle)
    laser_xy = []  # Laser data in cartesian coordinates (x, y)
    for i in range(180):
        # i contains the index of the laser ray, which starts at the robot's right
        # The laser has a resolution of 1 ray / degree
        #
        #                (i=90)
        #                 ^
        #                 |x
        #             y   |
        # (i=180)    <----R      (i=0)

        # Extract the distance at index i
        dist = laser_data.values[i]
        # The final angle is centered (zeroed) at the front of the robot.
        angle = math.radians(i - 90)
        laser_polar += [(dist, angle)]
        # Compute x, y coordinates from distance and angle
        x = dist * math.cos(angle)
        y = dist * math.sin(angle)
        laser_xy += [(x, y)]
    return laser_polar, laser_xy
```


