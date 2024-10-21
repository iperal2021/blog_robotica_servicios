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

With this (X,Y) values I can move the drone to the location. Once there, I need to perform a sweep to find as many survivors as possible. To get a better control over the image I decided to crop the image into a circle.