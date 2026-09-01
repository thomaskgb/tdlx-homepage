---
title: 'Green means connected, not correct: building a running coach on Garmin data'
date: 2026-09-01T20:00:00+02:00
draft: false
---

I train for a half marathon off a spreadsheet. Every week has prescribed sessions with a target distance, a target duration and a heart-rate band, and next to them empty columns for what actually happened. Filling those columns by hand is exactly the kind of chore that quietly stops getting done, so I automated it: pull the runs out of Garmin, write the actuals into the plan, and generate a dashboard that tells me whether I am getting fitter.

The automation worked for months. Then it did not, and it took me an embarrassingly long time to notice, because everything was still green.

## Two hops, one of which lies

Getting your own data out of Garmin Connect is harder than it should be. There is no export worth the name, so the path runs through an open-source extractor, [garmin-givemydata](https://github.com/nrvim/garmin-givemydata), which logs in through a real browser and mirrors your activities into a local SQLite database. A second component, an [MCP](https://modelcontextprotocol.io/) server, reads that database and exposes it to Claude, so a weekly scheduled task can sync the plan and write me a summary.

Two hops. The important detail, which I did not internalise until it bit me: **only the first hop talks to Garmin.** The MCP server never leaves the machine. It reads the local database and answers cheerfully whatever is in it, including nothing at all.

So when the weekly task started reporting "0 runs synced", there were two independent faults hiding behind one symptom.

The first was a path. Both Claude configurations pointed at a Python launcher in `~/.local/bin`, which did not exist on this machine. The command line reported the server as connected the whole time, because there the binary resolves through `PATH` and works fine. The desktop app, which is what actually runs the scheduled task, does not inherit your shell `PATH`, so it needed an absolute path, and the absolute path it had was wrong. A green status in one client told me nothing about the other. That mismatch sat there for eight weeks.

The second fault was that the database had no data newer than early July, because the browser login was being blocked by bot verification and needed a human to sit in front of it. Fixing the path did not fix that, and would not have, ever. **A healthy server reading a stale database is byte-for-byte indistinguishable from a broken server.** Both return an empty list.

The fix is not clever, it is just loud. There is now a health check that runs before anything else and exits non-zero: it verifies the tool paths, all three places the server is registered, the protocol handshake, and the freshness of the database itself. The scheduled task calls it as a guard. If the data is old, the run fails with a sentence explaining which of the two hops broke, instead of succeeding with nothing to say.

## The part that is actually about running

With the data flowing again, the interesting question is whether any of it means anything. Aerobic fitness is supposed to show up as a better pace at the same easy heart rate, week over week. Mine looked like it was getting worse through the summer.

It was not. It was July.

![Weekly easy-run heart rate plotted against air temperature, with a dashed line showing heart rate adjusted to a common 15 degrees](/images/running-coach-heat.png)

Above roughly 20 °C a growing share of cardiac output goes to skin blood flow for cooling rather than to working muscle, so heart rate climbs at an identical pace. Plotting heart rate against air temperature makes the confound obvious: the two lines move together all season. The dashboard now also draws heart rate adjusted back to a common 15 °C, using a coefficient fitted from my own runs, so weeks in different weather can be compared at all. Read the solid line for what happened, the dashed line for what it means.

The bit I enjoyed most was working out where that adjustment does *not* belong. The other efficiency metric on the dashboard is metres covered per heartbeat, and my first instinct was to heat-correct that too. It turns out you must not. Heat raises heart rate *and* slows pace, and metres per beat divides distance by heartbeats, so both effects land in the same ratio and cancel. The data agrees: the correlation with temperature is near zero. Adjusting it would have corrected for an effect that is not there, and quietly manufactured a fitness trend out of the weather.

That coefficient is a model, not a measurement, and the chart says so. Temperature rose steadily across the season while my fitness also changed, so the two are partly collinear and the number absorbs some of that. It is a sanity check, never a truth.

## What I would keep

- **Silence is a failure mode.** Anything that can return an empty result needs to distinguish "nothing happened" from "I could not look". A guard that exits non-zero is worth more than the feature it protects.
- **Green in one client proves nothing about another.** GUI apps and shells do not share an environment. Check the config that the failing thing actually reads.
- **Code in git, data out of it.** The repository holds functions only. The workbook, the athlete profile and the generated reports live in a drive folder, bridged by one module. It keeps the code shareable and the personal numbers where they belong.
- **Before correcting for a confounder, check it is in there.** The heat adjustment and the decision not to apply it to metres per beat came from the same afternoon, and only one of them was obvious at the start.

More on the setup is on the [Running Coach project page](/projects/running-coach/). The repository is private and the numbers in the screenshots are scrambled, but if you want to talk through the pipeline, [reach out](mailto:missions@tdlx.nl).
