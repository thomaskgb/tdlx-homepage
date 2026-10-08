---
title: 'Havoc: a ledger for everything I said I would do'
date: 2026-10-08T09:00:00+02:00
draft: false
---

The most dangerous sentence in my Slack history is "I'll have a look." I type it a few times a day, I mean it every time, and the only place it gets stored is my head. My head turns out to be a poor database. It has no search, no backups and no due dates, and it likes to run its consistency checks at three in the morning, when it suddenly wants to know whether I ever answered that question from last Tuesday.

So I built a ledger. The repository is called havoc, which is either ironic or an honest description of what it replaced. It catches every small promise I make, every question I am asked and every review waiting for me, puts them in one list, and keeps checking that list against reality so that I do not have to.

![Overview: obligations said in Slack, a Claude session, GitLab and Todoist are captured by the sweep, a direct add or plain scripts, land in one ledger and show up on the board, where I act on them. A dashed loop re-checks open items against the thread, Slack, sent mail and calendar.](/images/havoc-flow.svg)

## Why a ledger and not a to-do list

A to-do list holds what I plan to do. A ledger holds what I owe, and that is a different thing. Entries arrive whether I planned them or not, every entry points at the message that created it, and nothing is ever erased. An item is either settled (done) or written off (dropped), and a write-off needs a reason. That last rule sounds bureaucratic, but it is the only record of what I chose to let go, and it is the difference between "I decided not to" and "I forgot".

The productivity gain does not come from doing more. It comes from carrying less. An open promise keeps costing a bit of attention until it is written down somewhere you trust, and the important word there is trust. A list I have to double-check is not offloading, it is a second job. Almost every design choice in havoc exists to protect that trust: an item is only closed with evidence, a date I set myself is never moved by a machine, and the same Slack message read on four sweeps still produces one item and one notification.

It also does a surprising amount of the boring work itself. A model reads Slack and decides whether "let me check that" was a promise. A second run re-reads older items to see whether they were quietly settled. A third one drafts the next step for each open item, so that in the morning I mostly approve, edit or reject. What it never does is send anything. Every run is read-only on Slack, mail and Notion, every send path is explicitly denied, and Approve on the board does not post a thing. I paste and I send, because what goes out under my name is still mine.

## Step 1: capture, with a wide net

Things land in the ledger from four places.

- **Slack**, four times a day, through the sweep: a headless Claude run that reads my messages, the messages sent to me and every thread I posted in, and decides what counts. Whether a sentence is a promise is a judgement call that no query can make, so this is the one place where a model does the capturing.
- **GitLab**, where the merge requests waiting for my review are mapped onto the ledger by a plain script. A review is not a judgement call, GitLab simply knows.
- **Todoist**, which stays my quick-capture inbox on the phone and the laptop, even offline. A small container on our home server reads it every ten minutes.
- **A chat with Claude**, when I commit to something in the conversation itself. That goes straight in.

The sweep sorts what it finds into eight kinds.

| Kind | What it means |
| --- | --- |
| commitment | I said I would do something. |
| request | Someone asked me to do something and I have not said yes or no. |
| question | Someone asked me something and I never answered. |
| awaiting | I asked someone for something and they went quiet. |
| owed to me | Someone promised me something that never arrived. |
| product request | A product ask aimed at me that is not a ticket yet. |
| review | A merge request waiting for my review. |
| unattended | A post in a product feedback channel that nobody picked up. |

Request is the one to watch. Those are the asks that turn into commitments without me ever deciding to take them on, which is exactly how a week fills up. The next action on a request is a decision (accept, hand off or decline), not execution.

The net is deliberately wide. A list I prune is better than a promise I drop, and when the list catches the wrong things, the fix is a sentence in the prompt rather than more code.

## Step 2: triage, with a clock on everything

Captured is not the same as accepted. Every item lives somewhere in this diagram:

![The life of an item: from Inbox, triage moves it to Open. Open can be handed off to Waiting and comes back when they answer. Closing an Open item sends it to Being checked, which ends in Done when evidence is found, Dropped when a reason is given, or back to Open without evidence. Moving the date of an Open item counts as a push, and two pushes make it drifting. Won't do needs a reason from any state.](/images/havoc-lifecycle.svg)

On the home side, triage is literal. A quick add from Todoist sits in the **Inbox** block on the board until I give it a project or a date, or drop it. On the work side, the sweep does a first pass of triage for me and proposes three things per item, each with a one-line reason.

- **A priority, ranked against the week**, not against how urgent the message sounded. P1 means someone is blocked on me or a customer is waiting. P2 fits in the gaps this week. P3 is for when there is room. My own setting always wins.
- **A handler.** Can an agent draft the next step, is it a decision only I can make, or is it simply not for this week?
- **A due date.** If the message named one, that one wins. Otherwise every kind gets a default clock.

![Default due dates: one working day for a request, a question or a review, three for a commitment, five for anything else, ten for a to-do without a date.](/images/havoc-clocks.svg)

A reply and a review are a day's work, so they get one day. A commitment gets three. Any date I set myself gets a "your date" marker, and no sweep will ever move it.

Then there is the waiting column. "Waiting on them" is a status of its own, never counted as open and never in the overdue block, because the next move is not mine. When its date passes, the row does not turn red. It says "no answer for 4 days, chase", which is a much nicer thing to read in the morning.

## Step 3: the feedback loops

Capture and triage would make a decent to-do app. The loops are what make it a ledger I trust, because a list that only grows becomes a list I stop reading. Havoc runs four of them, each on its own clock.

![Four feedback loops drawn as rings around the ledger: the Todoist sync every ten minutes, sweep and resolve four times a day, the shift after every sweep, and the weekly review that tunes the prompt.](/images/havoc-loops.svg)

**Every ten minutes: the Todoist sync.** Completing a task on my phone closes the row in havoc, and closing a row on the board completes the task in Todoist. Dates and priorities follow Todoist until I touch them in havoc. From then on havoc wins and pushes them back, so the two always agree.

**Four times a day: sweep and resolve.** After each sweep, a second run takes a batch of fifteen open items (overdue first, then the ones checked longest ago) and re-reads each of them in four places: the original thread, Slack elsewhere, my sent mail and my calendar. Every item gets one of five verdicts: done, dropped, nudged (I chased it, so the clock restarts), moved on (the ask changed, so the item is reworded), or still open. It never closes an item because it found nothing. An item closed by mistake is an item I never see again, and that is the worst failure this system can have. Four runs of fifteen re-read a sixty-item ledger every day, which is more diligence than I have ever managed by hand.

The same rule applies to me. When I click **Done** on the board, the item is not closed yet. It gets a "being checked" chip while a quiet session looks for the deliverable in Slack, mail or Notion. My click is a claim, and the evidence turns it into a fact. Without evidence the item stays open, and if the thread shows that something else is still owed, that becomes a new item.

**After every sweep: the shift.** The shift picks up to five open items that the agent can handle and runs one headless session per item, once after every sweep and again at 18:07 for whatever is left. Each session reads the thread, searches Slack and Notion for what the answer depends on, and leaves a proposal: ready to approve, a draft plus the one question that decides it, or handed back to me with what it found. In the morning there is a Proposals block at the top of the board, and most items take one glance.

**Every week: the review.** Every proposal keeps its history, including whether I approved, edited or rejected it, and a rejection requires a reason. Once a week those notes become the material for tuning the shift's prompt. The prompt lives in git, which makes it the agent's memory, and every change to it is a diff I approve rather than a silent drift.

And then there is one loop for my own bad habits: **drift**. Every time I move a date, the ledger counts a push. After two pushes the item is drifting and gets a chip on the board. On the third attempt it does not get a fourth push. It gets a concrete next step and a date, it gets dropped, or it gets handed to someone. "Set up the sales training" drifts forever, while "book 45 minutes on Thursday to pick the training format" gets done.

## A day with havoc

![A weekday from 7:00 to 20:00: the Todoist sync ticks every ten minutes, sweeps run at 8:37, 11:37, 14:37 and 17:37, the shift follows each sweep and runs once more at 18:07, and my own part is judging drafts in the morning plus quick adds whenever.](/images/havoc-day.svg)

In practice I barely notice it. The sweeps run at odd minutes past the hour, the Todoist sync ticks along in the background, and I get at most two notifications per item: one when it is captured and one when it goes overdue. My part is a few minutes in the morning to judge the drafts, a quick add whenever something pops into my head, and the occasional **Pick up**, which opens a terminal with a Claude session that is already briefed on the item.

## Under the hood

The work ledger is a JSON file on my work laptop, served as a board on localhost. That is a deliberate choice. An earlier version mirrored the ledger to Notion through a model run, and a close I made by hand then sat on the board for hours while the same list lived in two places. Now the board reads the one file on every request, so it cannot be out of date.

The home side is havoc proper: one SQLite file behind a small HTTP API, running in a container on our home server behind Cloudflare Access. A few choices I am happy with:

- **The client picks the id, and every write is idempotent.** Sending the same update twice leaves one row, so a retry after a dropped connection is always safe and a device can queue its writes while offline.
- **There is no delete.** An item that will not happen is dropped, with a reason.
- **There is one token per device**, stored only as a hash, so a lost laptop is one line removed.
- **It fails closed.** If the hostname ever loses its Access policy, requests without Access's signed header are refused instead of being served to the internet.
- **Work items are stored thin.** The quote that created an item stays on the work laptop and never reaches the home server.

## What I learned

- Offloading only works if you trust the list. Every feature that made the list more trustworthy did more for my head than every feature that made it smarter.
- Use a model for judgement and plain code for arithmetic. Deciding whether "I'll check" is a promise needs a model. Due dates, identity and notifications do not, and they are much easier to debug without one.
- Never close on silence. "I found nothing" is not evidence that something was done.
- Make letting go explicit. Dropping something with a reason feels a lot better than watching it rot at the bottom of a list.
- Keep the prompt in git. When the agent gets something wrong, the fix is a reviewed change, not a hope that it will do better next time.

The repository is private, since half of it is wired into my work tools. If you are building something similar, or want to argue about whether a ledger is just a to-do list in a suit, [send me a mail](mailto:missions@tdlx.nl).
