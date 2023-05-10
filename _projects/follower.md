---
layout: page
title: Line Follower
description: A line following maze solving robot
img: assets/img/follower/cover.jpg
importance: 4
category: work
---

In 2019 as part of our Mechatronics Design course (EEE3061W) we get to design a robot in teams of 4 which has to compete at some objective. It's a notoriosly challenging course with late nights, but also incredibly fun, creative and rewarding. This year we were tasked with designing a line following robot which would map out an unseen maze and solve it as fast as possible. In the team I was responsible for the mechanical design of the robot and also the embedded software running on the STM32F0 microcontroller.

<div class="row">
    <div class="col-sm-3 mt-3 mt-md-0"></div>
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.html path="assets/img/follower/cover.jpg" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-3 mt-3 mt-md-0"></div>
</div>
<div class="caption">
    Our awesome little line following, maze solving robot!
</div>

The part of the embedded software I found most interesting was the maze solving algorithm. We would have a chance for our robot to explore the maze and then a second run to navigate through the fastest route and solve it in the shortest time possible. Crucially the maze would not have any loops to keep things only moderately challenging.

I opted to use [Lee's Algorithm](https://en.wikipedia.org/wiki/Lee_algorithm) because once the maze has been fully traversed it is guaranteed to have found the shortest route. It is also simple to implement in C++ and simplicity was key for keeping bugs to a minimum! The maze was stored in a 2D array with each element corresponding to a physical node in the maze. As the robot moves along a branch from the start point it increments a counter and stores the value at the correwsponding node. If a route goes over an existing value it can only be overwritten of the value is smaller. This effectively produces a distance map along all of the paths. Once you have reached the end of the maze you can then traverse along the maze simply by following the lowest distance values! It's an awesome alogithm and I loved the simplicity. The main downside is that you need to store the whole maze so we were very quickly bumping up against the memory limit in the STM32F0 and had to simplify a few things.