---
title: Security Station
date: 2026-04-01
draft: false
weight: 5
summary: 'Automated security audits of my own infrastructure: host audits, outside-in network scans and secrets hygiene, run as AI agent skills.'
tech: ['Claude Code skills', 'nmap / nuclei', 'gitleaks', 'Docker']
---

## What it is
If you self-host your smart home, your cloud and a public product, you had better audit them. Security Station is a small toolkit of AI agent skills that audit my own infrastructure the way an external reviewer would, and produce curated, actionable reports.

## How it works
Three recurring audits, each packaged as a Claude Code skill:
- **On-host audit**: walks the home server's Docker containers and system services looking for misconfigurations, exposed ports and privilege issues.
- **Outside-in scan**: nmap and nuclei against my own public endpoints, to see exactly what an attacker on the internet sees.
- **Secrets & dotfiles audit**: gitleaks and targeted checks across repositories and SSH/dotfile hygiene.

The interesting part is the packaging: encoding a security review as a repeatable, documented skill means the audit actually happens regularly, instead of being a good intention.

## Why
It closes the loop on the other projects: build it, run it, then attack it yourself before someone else does.
