---
layout: page
title: Hopper
description: A legged research platform
img: assets/img/hopper/cover.jpg
importance: 3
category: work
toc:
  sidebar: left
---

## Introduction
At the end of 2019 I built a 2kg hopping robot for a PhD researcher as part of a 6-week internship for the UCT Robotics Lab. When I joined the lab full-time I took over the project again and ported the system over to the Lab's new Simulink Real-Time Target Machine using the CAN communication protocol, implemented impedance control, and built the Raibert-based control system for consistient hopping and velocity control. You can see the robot hopping and reacting to pushes in this video while it tries to stay in the same spot.

{% include youtube.html id="VbLtQkqtwZ0" %}

This video shows the first version of the hopper. I also built a newer version while working part-time alongside my masters which used improved actuators, had stronger and more rigid legs, and a much simpler and lighter design.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/hopper/cover.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    Redesigned hopper mounted on the boom.
</div>

## Control
Add controller details here...