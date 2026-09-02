---
title: 'Building a running coach on Garmin data'
date: 2026-09-01T20:00:00+02:00
draft: false
aliases: ['/posts/green-means-connected-not-correct/']
---

I am training for a half marathon, and the plan lives in a spreadsheet. Every week has the sessions I am supposed to run, with a distance, a duration and a heart rate zone, and next to them empty columns for what I actually ran. Filling in those columns by hand is exactly the kind of chore that should be automated, so I did. Pull my runs out of Garmin, write them into the plan next to what was prescribed, and generate a dashboard that answers one question: am I getting fitter?

## How it works

Garmin does not make it easy to get your own data. There is no useful export, so I use [garmin-givemydata](https://github.com/nrvim/garmin-givemydata), an open-source tool that logs in through a real browser and copies your activities into a local SQLite database. On top of that sits a small [MCP](https://modelcontextprotocol.io/) server that lets Claude read the database, so a scheduled task runs every week, syncs the plan and writes me a short summary of how training is going. A few Python commands do the actual work: sync the runs into the workbook, roll the plan forward, and render the dashboard as a single HTML file.

One rule from the start: the code lives in git, the data does not. The repository only has code. The workbook, my profile and the generated reports live in a drive folder, with one module in between. That keeps the code shareable and my heart rate out of GitHub.

## Am I getting fitter, or is it just the weather?

Fitness should show up as a faster pace at the same easy heart rate, week after week. My chart showed the opposite: over the summer it looked like I was getting slower.

I was not. It was July.

![Weekly easy-run heart rate plotted against air temperature, with a dashed line showing heart rate adjusted to a common 15 degrees](/images/running-coach-heat.png)

Above roughly 20 °C your body sends more blood to your skin to keep cool, so your heart rate goes up at the same pace. Once I plotted heart rate against air temperature, it was obvious: the two lines move together all season. So the dashboard now also draws heart rate adjusted to 15 °C, using a correction factor derived from my own runs. The solid line is what happened, the dashed line is what it probably means.

The more interesting question is where that correction does *not* belong. The other metric on the dashboard is metres per heartbeat, and the obvious move is to heat-correct that one too. You should not. Heat raises your heart rate *and* slows you down, and metres per beat divides one by the other, so both effects cancel out. The data agrees: the correlation with temperature is close to zero. Correcting it anyway would have invented a fitness trend out of the weather.

That correction factor is an estimate, not a measurement, and the chart says so. Temperature went up over the season while my fitness changed at the same time, so the number absorbs a bit of both. It is a sanity check, nothing more.

## Fail loudly

The pipeline has two steps, and only the first one talks to Garmin. The second one reads whatever is in the local database, and it is perfectly happy to read nothing at all. So when the Garmin login got stuck behind bot verification for a while, the weekly summary kept running and kept reporting zero runs, with every status light green. There is now a health check that runs before anything else, and the most important thing it checks is whether the database actually has recent data. If it does not, the task fails and says why, instead of quietly reporting nothing.

## What I learned

- Check the weather before you trust the trend. Heart rate against temperature explained a whole summer of "getting slower".
- Correct only where the effect is actually present. The same heat that pushed my heart rate up also slowed me down, and metres per beat already cancels that out.
- Code in git, personal data out of it. One module in between is enough.
- An empty result is not a healthy result. Anything that can return "nothing" needs to be able to say why.

More on the setup is on the [Running Coach project page](/projects/running-coach/). The repository is private and the numbers in the screenshots are scrambled, but if you want to compare notes on getting data out of Garmin, [send me a mail](mailto:missions@tdlx.nl).
