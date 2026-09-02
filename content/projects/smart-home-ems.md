---
title: "Smart Home & Energy Management"
date: 2026-07-01
draft: false
weight: 2
summary: 'A Home Assistant based system that orchestrates solar, battery, EV charging, heat pump and hot water to minimise our energy bill.'
tech: ['Home Assistant', 'MQTT / Zigbee', 'eBUS', 'Docker', 'InfluxDB / Grafana']
resources:
  - title: "Dynamic tariffs and the dishwasher"
    url: https://www.linkedin.com/feed/update/urn:li:activity:7467834682219397120/
    source: linkedin
    date: June 2026
    blurb: "A few years of hourly prices in Amsterdam taught me the manual version of this project, telling my wife \"not yet, wait, okay now\"."
---

![The EMS overview dashboard: live energy flows between solar, battery, grid and home, EMS signals, and quick toggles for every subsystem](/images/ems-overview.png)

## What it is
Our house runs on a self-hosted smart home stack with a custom Energy Management System at its centre. The EMS decides, hour by hour, where energy should flow: maximising solar self-consumption and shaving the peak that drives the Flemish capacity tariff, across the solar inverter and battery, the EV charger, the heat pump and the hot water boiler.

## How it works
- Home Assistant with ~18 custom configuration packages implements the logic: solar curtailment, EV charge scheduling, charger load balancing, inverter capping and peak shaving.
- Real hardware integrations: a solar + battery system, an EV charger, a Vaillant heat pump spoken to directly over eBUS, ventilation, and a fleet of Zigbee and ESPHome devices.
- Around it: Frigate for camera detection, fully local voice assistance (Whisper + Piper), and InfluxDB + Grafana for long-term energy analytics.
- Everything runs in Docker on a home server, versioned in git and deployed like any other software project.

A favourite example is the EV charge planner: it takes the kWh needed, the solar forecast and the cheapest grid hours, and plans the charge so the car is ready by the deadline at minimal cost, capped below the capacity-tariff peak.

![The EV charge plan card: 18.2 kWh needed, 16 kWh planned from solar and 2.2 kWh from the single cheapest grid hour, estimated cost 28 cents](/images/ev-charge-plan.png)

## Why
It started years ago with a few smart lights and grew into the most instructive engineering project I have: real constraints, real money on the line, and a household of end users who file bug reports at breakfast. The repository is private, but I am always happy to talk through the architecture.
