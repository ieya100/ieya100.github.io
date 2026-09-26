---
layout: default
title: Improving VRM Cooling on Huananzhi H12D-8D Ver2.0
description: A simple Huananzhi H12D-8D Ver2.0 VRM cooling modification using washers, an open heatsink and the stock fan to reduce airflow resistance and noise.
lang: en
translation_key: huananzhi-h12d-8d-vrm
alternate_url: /cases/huananzhi-h12d-8d-vrm-fan-mod.html
---

# Improving VRM Cooling on Huananzhi H12D-8D Ver2.0: Reducing Stock Fan Noise

This is a small modification to the CPU power-delivery cooling system on the **Huananzhi H12D-8D Ver2.0** motherboard for AMD EPYC processors and the SP3 socket. The goal is to expose the heatsink fins, give the stock fan a less restrictive air path and reduce its noise.

**Motherboard:** HUANANZHI H12D-8D Ver2.0  
**Platform:** AMD EPYC 7002/7003, Socket SP3  
**Topic:** VRM cooling, fan noise, VRM fan mod

## The original problem

In the factory configuration, the VRM heatsink is completely covered by a decorative plate, with a small fan mounted in its center.

<p align="center">
  <img src="/cases/images/huananzhi-h12d-8d/factory-cover.jpg" width="620" alt="Factory VRM cooling assembly with the heatsink covered by a plate">
</p>

At first glance, the plate appears to act as an air duct: the fan draws air from above, pushes it under the cover and sends it along the heatsink fins. Removing the cover revealed that the design does not quite work that way.

There is a recessed well directly under the impeller. Air can escape from it only through slots approximately one millimetre wide or even narrower. The stock H12 fan has a 30 × 30 mm body, is 10 mm thick and measures approximately 42 mm between opposite mounting points across the diagonal. It is rated for 5 V and 5000 RPM.

In the original configuration, the fan effectively blows into a confined well with a very small outlet area. Much of its effort is spent overcoming this restriction, while the fins hidden under the cover receive little benefit from the general airflow inside the chassis.

<p align="center">
  <img src="/cases/images/huananzhi-h12d-8d/heatsink-open-v2.jpg" width="520" alt="VRM heatsink after removing the upper cover">
</p>

<p align="center">
  <img src="/cases/images/huananzhi-h12d-8d/fan-h12-v2.jpg" width="520" alt="Stock H12 5 V fan measuring 30 by 30 by 10 mm and rated for 5000 RPM">
</p>

## What I changed

I removed the upper plate and mounted the stock fan directly on the heatsink. Three small washers with a combined thickness of approximately 1.5 mm were added under each mounting point. Together with a retainer made from cable sheath, they raise the fan approximately 2 mm above the recessed area.

This extra clearance reduces the restriction **downstream of** the impeller. The fan still draws air from above and blows it downward, but the air can now spread sideways more freely and reach the side fins. Removing the cover also exposes the complete heatsink to airflow from the chassis fans.

### A small assembly trick

Holding several washers on a mounting screw while positioning the fan is inconvenient. I made a simple retainer from the outer sheath of a twisted-pair Ethernet cable: a small piece was cut to roughly the diameter of the washers and a hole was made for the screw.

The sheath grips the screw, keeps the washer stack in place during installation and adds approximately another 0.5 mm of clearance.

<p align="center">
  <img src="/cases/images/huananzhi-h12d-8d/washers-holder-v1.jpg" width="520" alt="Three washers and a yellow Ethernet cable sheath retainer fitted to each fan mounting screw">
</p>

<p align="center"><em>The three washers provide approximately 1.5 mm of clearance, while the yellow cable-sheath retainer adds about 0.5 mm.</em></p>

A potentially cleaner alternative would be a compatible 30 × 30 mm fan with a height of 7 mm. Its upper surface might remain below the top of the heatsink even with the necessary clearance underneath. Before replacing the fan, verify its supply voltage, connector polarity, airflow direction and mounting-hole spacing.

<p align="center">
  <img src="/cases/images/huananzhi-h12d-8d/fan-position-v2.jpg" width="520" alt="Test-fitting the stock fan directly on the VRM heatsink">
</p>

<p align="center">
  <img src="/cases/images/huananzhi-h12d-8d/final-installation-v2.jpg" width="520" alt="Final installation of the fan on the exposed VRM heatsink">
</p>

### Video

<div align="center">
  <video controls playsinline preload="metadata" width="420" poster="/cases/images/huananzhi-h12d-8d/final-installation-v2.jpg">
    <source src="/cases/images/huananzhi-h12d-8d/vrm-fan-demo-v1.mp4" type="video/mp4">
    Your browser does not support embedded video. <a href="/cases/images/huananzhi-h12d-8d/vrm-fan-demo-v1.mp4">Open the video file directly</a>.
  </video>
</div>

<p align="center"><em>The fan running after installation on the exposed heatsink.</em></p>

## Result

This motherboard does not appear to expose a VRM temperature sensor, so I cannot provide a measured temperature difference. Subjectively, the fan became noticeably quieter, most likely because the lower back pressure allows air to leave the recessed area more freely.

The modification also passed a practical everyday test: the following night I was able to sleep without closing the door to the room containing the server. This is not a substitute for measurements with a sound-level meter, but it describes the difference in normal use rather well.

The modification provides four main benefits:

- the heatsink is exposed to the general chassis airflow;
- the outlet area beneath the fan is larger;
- air can reach the side fins;
- the small fan produces less noise.

## Possible next step

A future improvement could be a custom heatsink designed for a higher-quality and quieter Noctua 40 × 40 × 10 mm fan. The heatsink should be designed with adequate clearance under the impeller and unrestricted air channels leading toward the fins.

The current and future configurations could then be compared with a thermocouple or thermal camera under an identical sustained workload. Noise should be measured with a sound-level meter at a fixed distance.

## If you repeat this modification

If you try the same modification, please measure the temperature and noise level **before and after**, and share the results. I honestly do not feel like returning the already modified system to its original configuration solely for a comparative test 🙂

For a useful comparison, please include:

- ambient temperature;
- the VRM temperature measurement location and method;
- the workload and its duration;
- the distance between the sound-level meter and the chassis;
- the chassis fan configuration.

Results can be posted as a GitHub Issue or a comment below. I will add useful measurements to this article with credit to the author.

> Disconnect the system completely from power before attempting this modification. Make sure that the washers and screws cannot touch the motherboard, that the impeller rotates freely, and that the fan cable cannot reach the blades.

