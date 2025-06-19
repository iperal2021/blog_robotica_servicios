---
layout: default
title: Amazon
nav_exclude: false
---

## AMAZON WEREHOUSE

The objective of this practice is to implement the logic that allows a logistics robot to deliver shelves to the required place by making use of the location of the robot. The robot is equipped with a map and knows its current location in it. The main objective will be to find the shortest path to complete the task.

Another goal is to leran how to use the *OMPL* library.

## PLANING

To start planing I need to get the conversion of the coordinate from *3D* to *2D*. This time the exercise give us all the measurements of the map in both spaces. Once the transformation is correct is time to learn wich functionas of *OMPL* I have to use and what does each one.

Using the example from the documentation I can try to define the start point and the objetive coordinates to try the planner. The early tests show incorrect paths due to *OPML* use the X and Y coordinates the other way around, so where I use the X coordinate I have to use the Y, and vice versa.

![path]()

## PLAN EXECUTION

Once the path is complete the robot can start moving. For a good navigation I implemented a PID control over angular and linear speed. I use a simple *finite state machine* that use three states:

1. INIT: variables are initialized.
2. TURN: robot turn until it reaches the correct yaw. Only angular speed.
3. FORWARD: to move forward. In this state is controled both angular and linear speed.

Once the robot reaches the objetive it can start the second planing now with the shelf geometry. It uses the same movement.

## VIDEO

[![IMAGE ALT TEXT](http://img.youtube.com/vi/#####/0.jpg)]("Amazon Werehouse")

> Youtube URL if not displayed: []()