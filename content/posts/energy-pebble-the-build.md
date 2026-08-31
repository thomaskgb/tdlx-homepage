---
title: 'Energy Pebble, part 2: the build'
date: 2026-03-14T20:00:00+01:00
draft: false
---

So we had [the idea](/posts/energy-pebble-the-idea/) for the Energy Pebble: a simple light that shows you when energy is cheap and green versus expensive and fossil. Now we had to actually build it. Here is what the stack looks like for a side project built on evenings and weekends.

## Hardware
An ESP32 microcontroller with a ring of NeoPixel LEDs. The outer ring shows the coming hours, so you can see what is ahead, and the center dot shows the current price. All powered by USB-C. Total component cost? Around 15 euro per device.

## The case
3D printed. I went through about 6 iterations to get the shape right: a smooth pebble-like form that diffuses the light nicely. I ended up printing batches of 9 at a time (roughly 5-hour prints) when we needed to scale up to 16 units for our test group.

## Firmware
We used [ESPHome](https://esphome.io/), an amazing open-source project for ESP-based devices. It let us define the LED behaviour in YAML and push updates over-the-air. Yes, we could update the firmware on devices sitting in people's kitchens from our laptops. The firmware went through three versions: v1.0 (basic), v1.1 (device identification), v2.0 (full OTA support).

## The API
I built a custom API that pulls day-ahead price data from Elia's public grid data (Elia is the Belgian TSO). Every 15 minutes it fetches the latest prices, translates them into colour values, and serves them to the devices. The Pebble connects to your wifi and polls the API.

## The platform (this is where it got fun)
What started as "just an API" turned into a full management platform:

- User management: admins, regular users, device assignments
- Device management: see which devices are online, claimed, recently active
- Firmware management: push OTA updates, track 1300+ OTA check-ins
- API token management with scoped permissions

Was all of that necessary for a proof of concept with 10 users? Absolutely not. Did I enjoy building it? Absolutely yes.

The whole thing is open source: [energy-pebble-api](https://github.com/thomaskgb/energy-pebble-api) and [energy-pebble-esphome](https://github.com/thomaskgb/energy-pebble-esphome).

Next post: [what happened when we actually put these in people's homes](/posts/energy-pebble-what-we-learned/).
