---
layout: page
title: Shunt
description: An automatic shunt regulator
img: assets/img/shunt/cover.jpg
importance: 5
category: work
---

Two of the legged robots I have worked on use high torque BLDC motors coupled to a planetary gearbox as their actuators like the MIT proprioceptive actuators. These actuators are awesome, but have a small problem when running from a power supply rather than a battery pack. When doing negative work the motors will back-feed the power supply, raising the voltage on the output filtering capacitors and triggering the over voltage protection. This results in the robots shutting down and collapsing, typically during a particularly powerful manoever where lots of energy need to be dissipated. To fix this I built an automatic shunt regulator based off of the one I saw on the open-source [ODrive](https://odriverobotics.com/) motor controller. The shunt regulator works by detect this rise in PSU voltage and dumping the energy from the motors into a 50W 0.47Ω power resistor, essentially acting as a variable load and regulating the voltage. A microcontroller monitors the supply voltage with a voltage divider and regulates the amount of power going into the brake resistor by varying the duty cycle of a 20kHz PWM signal switching a MOSFET. The gate driver quickly charges the gate capacitance and ensures the MOSFET is turned on hard, minimising power dissipation during conduction.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/shunt/schematic.png" class="img-fluid rounded" %}
    </div>
</div>
<div class="caption">
    Simplified schematic of the shunt regulator circuit.
</div>

The PWM signal is linearly ramped up based on the bus voltage providing a smooth fade in or out as opposed to instantly dumping the maximum possible power, over 1.2kW, through the brake resistor for even just small voltage spikes. The duty cyce and ramp is determined with this line in the microcontroller code.
```c++
brake_duty = min(max((bus_voltage - overvoltage_ramp_start)/(overvoltage_ramp_end - overvoltage_ramp_start), 0.0f), 1.0f);
```
By configuring `overvoltage_ramp_start` and `overvoltage_ramp_end` in the firmware the supply voltage and responsiveness of the power dissipation can be adjusted.

<div class="row">
    <div class="col-sm mt-3 mt-md-0"></div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/shunt/cover.jpg" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0"></div>
</div>
<div class="caption">
    Working circuit on prototype board.
</div>