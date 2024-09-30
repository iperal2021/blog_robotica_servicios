---
layout: default
title: Localized Vacuum Cleaner
nav_exclude: false
---

# Localized Vacuum Cleaner

The first practice has the goal to to implement the logic of a navigation algorithm for an autonomous vacuum cleaner by making use of the location of the robot. The robot is equipped with a map and knows it’s current location in it. The main objective will be to cover the largest area of ​​a house using the programmed algorithm.

The algorithm chosed for this job is *BSA: Backtracking Spiral Algorithm*

### Localization

First thing I need is to relate the **3D** coordinates from *Gazebo* to the 2D map. The best way to do this is first work with the image, creating the grid I will use later. Once the grid is created by transform the array of the map into a matrix, I can start to get points.

<center>
    <img src="assets/img/grid_map.png" width="500" height="300">
</center>

* To get the coordinates I need to calcule using this ecuation:

<center>
    <img src="assets/img/ecuation.png" width="500" height="200">
</center>

Now I have to get some points to calculate the different values. For the *scale* I can chos 2 points with the same coordinate X or Y and the get the **3D** pose.

The grid is made with squares of 23x23 pixels, thanks to know this I can get a better approach. I chose the **3D** points (4.54 , -1.44) and (2.20, -1.44). This two points are the **2D** points (115, 276) and (368, 276). To get the *scale* I made the mean of the pair of coordinates. I get the follow:

* 2.3 meters of difference in the X **3D** coordinate
* 253 pixels from the X **2D** coordinate

