---
title: Energy Pebble
date: 2026-08-01
draft: false
weight: 1
summary: 'An ambient wall light that shows when electricity is cheap and green: hardware, firmware, API and web platform, built end-to-end.'
tech: ['ESP32 / ESPHome', 'Python / FastAPI', 'Docker', 'Home Assistant', '3D printing']
---

![The Energy Pebble glowing on a wooden wall, outer ring showing green, yellow and red hours](/images/pebble-device.jpeg)

## What it is
The Energy Pebble is a small wall light that answers one question at a glance: *is now a good moment to use electricity?* A ring of LEDs shows the current hour and the hours ahead in green, yellow or red, based on day-ahead electricity prices. No app to open, no dashboard to check. You just see it.

It started as an evening conversation with two friends who also work in energy, about why so few people have a dynamic electricity contract. Prices change every 15 minutes on the wholesale market and the savings are real, but checking apps and interpreting price graphs is a lot to ask of someone who just wants clean clothes. So we made the information ambient: a light on the wall. We built 16 devices, put them in about ten test households for several weeks, and it runs in production at [energypebble.tdlx.nl](https://energypebble.tdlx.nl).

## How it works
![Architecture: Elia day-ahead prices flow into the Energy Pebble API, which serves colour codes to the device over wifi, pushes OTA updates, and feeds Home Assistant](/images/energy-pebble-architecture.svg)

- The device is an ESP32-C3 with a 24-LED ring in a 3D-printed housing, running [ESPHome firmware](https://github.com/thomaskgb/energy-pebble-esphome) with guided provisioning and signed over-the-air updates.
- Behind it sits a [FastAPI platform](https://github.com/thomaskgb/energy-pebble-api) that converts day-ahead grid prices into simple hourly colour codes and serves a web dashboard, device administration and the firmware distribution channel.
- A Home Assistant integration exposes the same colour codes for home automations.

## My role
Everything: product, hardware, firmware, backend, deployment and the small community of test users. It started as an Arduino prototype and a 3D-printed case that took six iterations to get right, went through a build-in-public series on LinkedIn, and grew into a complete product with its own personalization roadmap. The full story is in three posts: [the idea](/posts/energy-pebble-the-idea/), [the build](/posts/energy-pebble-the-build/) and [what we learned](/posts/energy-pebble-what-we-learned/).
