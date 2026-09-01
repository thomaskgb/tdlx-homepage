---
title: Running Coach
date: 2026-09-01
draft: false
weight: 7
summary: 'A training pipeline that pulls my Garmin runs into a half-marathon plan and a self-contained analysis dashboard: code in git, personal data out of it.'
tech: ['Python', 'SQLite', 'MCP', 'openpyxl', 'Chart.js']
---

## What it is
A small toolchain that keeps a half-marathon training plan honest. It pulls my runs out of Garmin, writes the actuals back into the plan workbook next to what was prescribed, and generates a dashboard that answers one question: am I actually getting fitter, or is the weather just being kind?

![The generated training dashboard: key numbers, weekly volume and run frequency, easy-run heart rate against air temperature, aerobic efficiency, a heat scatter plot and a goal ladder](/images/running-coach-dashboard.png)

The numbers in that screenshot are deliberately scrambled. The shape of the charts is real, the values are not.

## How it works
- Garmin Connect has no usable export, so an open-source extractor ([garmin-givemydata](https://github.com/nrvim/garmin-givemydata)) logs in through a real browser and mirrors activities into a local SQLite database.
- An [MCP](https://modelcontextprotocol.io/) server exposes that database to Claude, so a weekly scheduled task can ask questions about my training in plain language instead of me writing a new query every time.
- Python commands do the deterministic work: sync runs into the workbook, roll the plan forward on its rotation schedule, pull Garmin activity comments into the notes column, and render the dashboard as a single self-contained HTML file.
- A health check runs first and exits non-zero on failure. It verifies the tool paths, all three Claude configs, the MCP handshake and, most importantly, whether the database actually has recent data in it.

The split that makes it work: **the code lives in git, the data never does.** The workbook, the athlete profile and the generated reports sit in a cloud drive folder, and one module bridges the two. That keeps the repository shareable and the personal numbers out of it.

## Why
Every running app will happily tell you that you ran 8 km. Very few will tell you whether your easy pace at a given heart rate improved, or whether it only looks that way because it was 12 °C instead of 27 °C. Those are the questions a plan should be steered by, so I built the smallest thing that could answer them, and learned more about silent failure modes than about running along the way. That story is in the post: [Green means connected, not correct](/posts/green-means-connected-not-correct/).
