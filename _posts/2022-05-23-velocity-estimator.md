---
layout: post
title: Estimating encoder velocity
date: 2022-05-23 00:00:00
description: A better way to estimate velocity with quadrature encoders.
thumbnail: assets/img/exposed-optical-encoder.jpeg
categories: Electronics Control
---

This is a really interesting method for estimating encoder velocity which I stumbled upon [here](). It's also used in the ODrive motor controller firmware. It uses a tracking loop to follow the encoder measurement with a PI feedback loop and finite bandwidth. What you end up with is filtered position and velocity estimates which you can use for control.

For some background and context as to why encoder velocity estimation is not trivial, quadrature encoders track position and direction by sensing evenly spaced markings on tracks, kind of like measuring distance by counting paces except on a much smaller scale. On the rotary encoders I have worked with these markings are made with either tiny gaps cut into a metal disk or incredibly small opaque patches etched onto a glass disk, and are picked up with an array of IR sensors to detect whether the light passes through the disk or is blocked. These markings can get mind-blowingly small. Typically two tracks of markings 90° out of phase are used which has two benefits.
```
               _______         _______       
Track A ______|       |_______|       |______
           _______         _______         __
Track B __|       |_______|       |_______|  
```
1. Resolution is doubled without gaps needing to be manufactured smaller.
2. Direction can be detected based on which track leads or lags. (Mainly this one)

Measuring the angular position and direction of an encoder is straightforward and can be done by using a microcontroller to keep count of the rising and falling edges for each of the tracks. Position is only known accurately at each edge, which for most applications is fine. Typically people assume a zero-order hold and recycle the same position measurement until the next edge is detected.

However, this becomes problematic when trying to estimate the velocity of the encoder. Average velocity is calculated using $\frac{\Delta x}{\Delta t}$ and with this discrete system you have the option of fixing either $\Delta x$ or $\Delta t$.

A fixed $\Delta t$ or sample frequency is often the easiest implementation on a microcontroller, but becomes an issue for low resolution encoders or slow movements when the $\Delta t$ is significantly smaller than the time between pulses. With multiple samples between edges this results in a stream of zero speed measurements followed by a velocity spike when an edge is detected and then more zero's - not ideal for control.

Alternatively a fixed $\Delta x$ is a bit harder to implement needing a hardware timer to measure the $\Delta t$ between pulses, but will provide the most accurate velocity average from the given information. This approach has problems with very high resolution encoders and fast speeds where more and more microcontroller clock cycles will be spent in interrupt handlers. Also the stationary case where the timer will overflow needs to be handled separately and often you might not be able to hook the encoder up to a fast timer if separate hardware is tracking encoder pulses.

Also need to mention that discrete position measurements and noisy velocity values result in pretty rough control and just arbitrarily filtering these values is never a good approach.

## Tracking loop
There is a very neat alternative to both of these methods which uses a tracking loop and feedback to follow the encoder pulses. This method is used in the ODrive motor controller firmware and is also talked about [here](https://www.embeddedrelated.com/showarticle/530.php). The tracking bandwidth can be adjusted for the encoder resolution and anticipated speed. Because it is a PI loop is has the benefit of providing both position and velocity estimates between encoder edges. Also handling the stationary case comes for free and it is not a headache to implement.

This is all the C++ code you need.

```cpp
void PLL::update(int32_t encoder_pos) {
    position += dt * velocity;
    float pos_error = (float)(encoder_pos - (int32_t)floor(position));
    position += dt * Kp * pos_error;
    velocity += dt * Ki * pos_error;
}
```

The position and velocity variables have units of ```counts``` and ```counts/s```. This feedback loop update needs to be called fast enough to meet the desired tracking bandwidth. ODrive runs it at 20kHz which seems good enough for most applications.

## Selecting Kp and Ki

The gains for the PI tracking loop are selected to ensure the response is critically damped - fast with no oscillations. The system can be written in the linear time-invariant form.

$$ \mathbf{\dot{x}}(t) = \mathbf{A}\mathbf{x}(t) + \mathbf{B}\mathbf{u}(t) $$

Like this

$$ \begin{bmatrix} \dot{p} \\ \dot{v} \end{bmatrix} = \begin{bmatrix} -Kp & 1 \\ -Ki & 0 \end{bmatrix}\begin{bmatrix} p \\ v \end{bmatrix} + \begin{bmatrix} Kp \\ Ki \end{bmatrix} \begin{bmatrix} p_e \\ 0 \end{bmatrix} $$

By looking at the eigenvalues of the $\mathbf{A}$ matrix we can place the poles and determine the natural response of the system.

$$ pole = \frac{-Kp}{2} \pm \frac{\sqrt{Kp^2-4Ki}}{2}i $$

For tracking to be critically damped the imaginary component should be zero, and the real component will determine the tracking bandwidth.

$$ Ki = \frac{Kp^2}{4} $$

$$ Kp = 2\cdot Bandwidth $$