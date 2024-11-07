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

Just before the `dist` variable of the code, I added an *if* sentence to avoid the *inf* measurements. This function allow me to solve two problems:

* The linear movement
* Check if there is space to park

The easy of the two is to check if there is enough space to park, but I will explain it later.

Using the coordinates I get from this function I'll create a straight line using the obstacles and then line up with the my *Y* axis to mantain a straihgt move. To do it I have to get the angle between my car and the obstacles. Then, with trigonometry and the straight line ecuation I calculate the slope to use the `atan`funtion to get the angle (in radians) that is going to be used as the error for the *P* controller

Here we can see how the car aligns itself even when starting turned.

<center>
    <img src="assets/img/alineacion.gif" width="300" height="500">
</center>

### Check for parking

To check if there is a parking with enough space for the car I use the values *(X,Y)* that I get from the `parse_laser_data()`function. With the coordinates of the laser I create a virtual rectangle with my desired measurements and while It's not empty, the car continue moving straight. When the space is enough, there's no points inside the rectangle so the car can park there.

```python
def check_parking(right_laser_xy):
    global parking_found
    
    right_side_obstacles = []
    for point in right_laser_xy:
        # If two check for points inside rectangle.
        if 0 < point[0] < X_max and Y_min < point[1] < Y_max:
            right_side_obstacles.append(point)
    
    # If right_side_obstacles is empty the car has found a parking
    if not right_side_obstacles:
        parking_found = True # Global Variable to controll FSM states
```

### Parking Maneuver

Once the car has found a space to park It have to start the maneuver. The steps to follow to park correctly are these:

1. start by placing the car to the right of the vehicle in front.

> There is a possibility that there is no car in front, so we will move using time and distance

1. Move backward and turn until the car is at 45º.

1. Once in the correct orientation move backward and turn in the opposite direction as before until the car is prallel to the init orientation

1. Check if there are enough distance in the front and in the back from possible obstacles and rectify if not.

