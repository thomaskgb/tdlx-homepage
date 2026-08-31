---
title: 'Cumulus: Personal Cloud'
date: 2026-03-01
draft: false
weight: 3
summary: 'A self-hosted private cloud on a single server: reverse proxy, VPN, auth, monitoring and push notifications. It also serves this website.'
tech: ['Docker Compose', 'Traefik', 'WireGuard', 'Prometheus / Grafana', 'Authelia']
---

## What it is
Cumulus is my personal cloud: one cloud server that hosts everything I want reachable from the internet under `tdlx.nl`, including this website and the Energy Pebble platform.

## How it works
![Architecture: internet traffic enters through Traefik with Let's Encrypt TLS and is routed to the website, Energy Pebble, Grafana, ntfy and Authelia, with a WireGuard tunnel to the home server](/images/cumulus-architecture.svg)

- Traefik as the reverse proxy with automatic Let's Encrypt certificates for every service.
- Authelia for single sign-on in front of private services, WireGuard as the VPN back to home.
- Prometheus and Grafana for monitoring, ntfy for push notifications from any script or service to my phone.
- Everything is Docker Compose, declarative and versioned in git: a new service is a folder and a few labels, and it gets routing, TLS and monitoring for free.

## Why
Running my own infrastructure keeps me honest about what "just works" really costs, and it is the foundation the other projects deploy onto. When Energy Pebble needed hosting, OTA firmware distribution and monitoring, the platform was already there.
