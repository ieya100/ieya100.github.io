---
layout: default
title: ASUS ProArt PX13 Power Connector Fire
description: A first-hand report of an ASUS ProArt PX13 proprietary power connector catching fire under load, with damage photos and possible explanations.
lang: en
translation_key: asus-proart-px13-power
alternate_url: /cases/asus-proart-px13-power-connector-fire.html
image: /cases/images/asus-proart-px13-connector-damage-1.jpg
date: 2026-09-26
last_modified_at: 2026-09-26
---

# ASUS ProArt PX13 Power Connector Fire

This page preserves my Reddit report about the proprietary power connector on an **ASUS ProArt PX13** catching fire. The original post generated a useful discussion but was later hidden by Reddit's automated filters, so I am archiving the account and photographs here.

**Device:** ASUS ProArt PX13  
**Configuration:** AMD Ryzen AI 9 HX 370, NVIDIA GeForce RTX 4060  
**Power adapter:** original 200 W unit  
**Original discussion:** [Reddit — The power connector caught fire!](https://www.reddit.com/r/ProArt_PX13/comments/1lbgb4a/the_power_connector_caught_fire_am_i_the_only/)

## What happened

I wanted to play some Counter-Strike and connected the original power adapter so the discrete RTX 4060 could be used. After a few minutes, smoke began coming from the power connector area.

When I pulled the plug out, it continued burning brightly, almost like a flare. The reaction stopped only after I disconnected the adapter from the AC outlet. The laptop had been used for approximately nine months without anything similar happening before this incident.

<p align="center">
  <img src="/cases/images/asus-proart-px13-connector-damage-1.jpg" width="520" alt="Melted proprietary power socket on an ASUS ProArt PX13 after the fire">
</p>

<p align="center"><em>Damage to the power socket on the ASUS ProArt PX13.</em></p>

<p align="center">
  <img src="/cases/images/asus-proart-px13-connector-damage-2.jpg" width="520" alt="Burned plug from the original ASUS ProArt PX13 power adapter">
</p>

<p align="center"><em>The melted and burned plug on the original power adapter.</em></p>

## What the photographs can and cannot show

The damage is concentrated around the electrical contact area and affects both the plug and the laptop socket. The photographs alone cannot establish the root cause.

One suggestion in the discussion was that the connector may not have been fully seated. A poor contact increases resistance, and at high current even a small resistance can produce intense local heating. This is a plausible scenario, but the connector position before the incident can no longer be verified.

There is also an important protection question: the adapter continued supplying energy after the plug had been removed from the laptop, and the burning stopped only after the adapter was unplugged from AC power. Such a failure is dangerous in a finished consumer product regardless of whether the connector had been seated perfectly.

## Why the proprietary connector raises questions

The PX13 uses a compact proprietary DC connector instead of a conventional barrel connector or USB-C Power Delivery. Participants in the Reddit discussion compared the incident with other high-power connectors, where incomplete contact engagement can also cause overheating.

This single case does not prove that the connector has a widespread design defect. It does document a real hazardous failure mode that PX13 owners should know about and that, in my view, deserves investigation by the manufacturer.

## Practical precautions for owners

- insert the plug fully and make sure it is not sitting at an angle;
- watch for dirt, looseness, discoloured plastic or unusual heat;
- do not use a connector or power adapter with visible damage;
- if you notice a burning smell, smoke or arcing, disconnect AC power as safely as possible;
- after an incident like this, do not reconnect the equipment—have both the laptop and adapter inspected by a qualified service centre.

> This page documents one personal incident. The exact cause has not been established, and the connector position before the fire was not recorded.

## What happened to the Reddit post

The post remains accessible by direct link together with its photographs and comments, but Reddit displays the notice: **“Sorry, this post was removed by Reddit’s filters.”** This indicates automated filtering; it does not identify the technical cause of the incident and is not evidence that ASUS requested the removal.

If you have experienced anything similar with an ASUS ProArt PX13, please share the details through GitHub Issues. The laptop model, adapter rating, workload, contact photographs and service-centre findings would be especially useful.
