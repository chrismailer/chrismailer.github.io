---
layout: page
title: Hexapod Adaptation
description: Bachelors thesis project
img: assets/img/hexapod/weird-gait.gif
importance: 4
category: work
---

In our final year of engineering at UCT we have the opportunity to spend the year doing a project we are interested in alongside our coursework under the guidance of an academic advisor. For my project I adapted the work from the paper [Robots that can adapt like animals](https://doi.org/10.1038/nature14422) to make a hexapod robot learn how to quickly adapt to a broken leg. The project is probably best documented in my [thesis](https://chrismailer.github.io/assets/pdf/mailer-bachelors-thesis.pdf).

For this project I built a physically realistic simulation of the robot using the [Bullet](https://pybullet.org/wordpress/) physics engine. You can find the simulation code [here](https://github.com/chrismailer/hexapod-sim). Using the simulator, approximately 40 million walking simulations were run on South Africa's 1.3 petaFLOPS Lengau cluster to find different ways of walking which used each of the legs in different amounts.

The 55 thousands hours of walking experience in simulation was then transferred to the physcial hardware enabeling it to change the way it walks and adapt to external influences such as hardware failure or different terrain.

{% include youtube.html id="VrYcNe66Zss" %}
