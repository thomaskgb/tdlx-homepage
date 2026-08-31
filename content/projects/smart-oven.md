---
title: Smart Oven
date: 2026-03-01
draft: false
weight: 4
summary: 'Retrofitting a regular oven with an ESP32, a thermocouple and PID control: precise temperatures and safety cutoffs included.'
tech: ['ESP32 / ESPHome', 'PID control', 'MAX31855 thermocouple', 'Home Assistant']
---

## What it is
A regular oven, upgraded with a brain. An ESP32-S3 reads the actual oven temperature through a MAX31855 thermocouple and drives the heating element with a PID controller, so instead of the wobbly thermostat cycles ovens normally do, it holds the setpoint precisely. Great for slow cooking, proofing dough and anything where a stable temperature matters.

## How it works
![Control loop: the thermocouple measures oven temperature, the ESP32 PID controller drives the heating element, with an independent safety cutoff above the loop and Home Assistant for setpoint and monitoring](/images/smart-oven-loop.svg)

- ESPHome on an ESP32-S3, with relays for the heating element, fan and light, and a door sensor.
- A PID climate controller with autotune keeps the temperature on target.
- Safety first: an independent over-temperature cutoff and sensor-failure handling sit above the control logic: if anything looks wrong, the heater goes off.
- Fully integrated in Home Assistant over MQTT, so the oven can be preheated remotely and monitored like any other device in the house.

## Why
Because a PID-controlled oven is strictly better than a normal one, and because projects that combine mains voltage, heat and software force you to think properly about failure modes.
