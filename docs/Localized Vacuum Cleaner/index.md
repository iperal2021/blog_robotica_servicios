---
layout: default
title: Localized Vacuum Cleaner
nav_exclude: false
---

# Localized Vacuum Cleaner

The first practice has the goal to to implement the logic of a navigation algorithm for an autonomous vacuum cleaner by making use of the location of the robot. The robot is equipped with a map and knows it’s current location in it. The main objective will be to cover the largest area of ​​a house using the programmed algorithm.

The algorithm chosed for this job is *BSA: Backtracking Spiral Algorithm*

### Localization

First thing I need is to relate the 3D coordinates from *Gazebo* to the 2D map. The best way to do this is first work with the image, creating the grid I will use later. Once the grid is created by transform the array of the map into a matrix, I can start to get points 

<center>
    <img src="assets/img/grid_map.png" width="500" height="300">
</center>