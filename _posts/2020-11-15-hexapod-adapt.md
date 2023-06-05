---
layout: post
title: A robot that can adapt like an animal
date: 2020-11-15 00:00:00
description: My bachelors thesis project
thumbnail: assets/img/hexapod/cover.jpg
tags: legged-robot simulation control
---

In our final year of engineering at UCT we have the opportunity to spend the year doing a project we are interested in alongside our coursework under the guidance of an academic advisor. For my project I adapted the work from the paper [Robots that can adapt like animals](https://doi.org/10.1038/nature14422) to make a hexapod robot learn how to quickly adapt to a broken leg. The project is best documented in my [thesis](https://chrismailer.github.io/assets/pdf/mailer-bachelors-thesis.pdf). I also published a [paper](https://dl.acm.org/doi/abs/10.1145/3449639.3459271) on the work.

For this project I built a physically realistic simulation of the robot using the [Bullet](https://pybullet.org/wordpress/) physics engine. You can find the simulation code [here](https://github.com/chrismailer/hexapod-sim). Using the simulator, approximately 40 million walking simulations were run on South Africa's 1.3 petaFLOPS Lengau cluster to find different ways of walking which used each of the legs in different amounts.

The 55 thousands hours of walking in simulation produce an experience map which was then transferred to the physcial hardware enabeling it to change the way it walks and adapt to external influences such as hardware failure or different terrain. The robot adapts quickly by trying gaits in the experience map, and based on its actual performance updates what walking behaviours it thinks will do better than others.

{% include youtube.html id="3KyUpPa7iBk" %}
