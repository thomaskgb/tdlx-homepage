---
title: 'One arbiter for the whole house: building our home EMS'
date: 2026-08-31T20:00:00+02:00
draft: false
---

Our house has a solar roof, a 10 kWh battery, an EV charger, a heat pump, a hot water tank and a dynamic electricity contract. Each of those came with its own app, and each app believes it is the main character. Left alone, the EV would happily drain the battery to get "cheaper" solar, the heat pump would fight the battery for the same surplus, and nothing would notice the 15-minute grid peak that drives the Flemish capacity tariff creeping up. So I built one arbiter: a custom Energy Management System on top of [Home Assistant](https://www.home-assistant.io/) that decides, minute by minute, where the power goes.

![The EMS overview dashboard: live energy flows between solar, battery, grid and home, EMS signals, and quick toggles for every subsystem](/images/ems-overview.png)

## How it thinks

The core is deliberately boring: a priority queue for solar surplus. Whenever the roof produces more than the house is using, the surplus goes down a ladder:

1. Fill the battery first, so tonight is covered.
2. Then the car, if it is plugged in. Surplus-only charging by default.
3. Then hot water, once the battery is comfortable.
4. Then a small comfort nudge to the floor heating.
5. Then a deeper hot-water boost, pushing the tank toward 70 °C.
6. Then dump whatever is left into discretionary loads, like the fridge's power-freeze mode.

Each step only runs if the step above it is satisfied. No surplus means nothing below the battery gets to run. On top of the ladder sit safety guards that can interrupt anything: don't blow the fuse, don't exceed the capacity-tariff peak, don't push more than 10 kW through the inverter, and force a 60 °C legionella cycle every 14 days no matter what the optimiser thinks.

My favourite piece is the EV charge planner. Give it the kWh needed and a deadline, and it combines the solar forecast with the cheapest grid hours to plan a charge that is ready on time at minimal cost, capped below the tariff peak. If the sun underdelivers, a state-of-charge guarantee tops up from the grid anyway.

![The EV charge plan card: 18.2 kWh needed, 16 kWh planned from solar and 2.2 kWh from the single cheapest grid hour, estimated cost 28 cents](/images/ev-charge-plan.png)

## Does it work?

This June the house earned about €20 on electricity instead of paying a bill, with grid consumption near zero. Since spring the EMS also watches injection prices: when the grid pays negative prices for exported solar, it curtails instead of paying to give energy away. The next step is proper arbitrage, charging the battery in the cheapest hour and selling it back in the most expensive one.

## What building it taught me

- **Predictable beats clever.** The priority chain is explainable at any moment: you can always answer why something is or isn't running. Every time I made the logic smarter than that, I regretted it during debugging.
- **Real constraints are the best teacher.** Fuse limits, tariff structures, inverter ceilings and a household of end users who file bug reports at breakfast make this the most instructive engineering project I have.
- **Silence is a failure mode.** A sensor that quietly stops reporting can switch off a feature without anyone noticing, so a watchdog is next on the list.

The full setup, including the hardware that is connected and the software stack around it, is on the [Smart Home & EMS project page](/projects/smart-home-ems/). The repository is private, but if you want to talk through the architecture, [reach out](mailto:missions@tdlx.nl).
