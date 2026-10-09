---
title: 'Havoc: a ledger for everything I said I would do'
date: 2026-10-08T09:00:00+02:00
draft: false
---

"I'll have a look" is probably the sentence I type most on Slack. I mean it every time, and then I forget about half of them. Not because I don't care, but because the only place that promise is stored is my head, and my head is not very good at that job. It mostly reminds me at three in the morning.

So I built havoc. It reads my Slack a few times a day, picks out what I said I would do, what people asked me and what I never answered, and puts it all in one list, next to the merge requests waiting for my review and my own to-dos from Todoist. The name comes from SEAL Team, where Havoc is the call sign of the operations centre that keeps track of everyone in the field. My field is mostly Slack.

<!-- SCREENSHOT 1: the full board, Work view -->

## Why a ledger

I call it a ledger rather than a to-do list because items arrive whether I planned them or not, and they never disappear. When something is finished it is closed as done, and when I decide not to do it, it is closed as dropped, with a reason. That reason felt like overhead at first, but it is now the part I like most. It is the only record of what I chose to let go of, and it turns "I forgot" into a decision.

The real gain is that I stopped keeping score in my head. That only works if I trust the list, so most of the effort went into making it trustworthy: the same message read four times still gives one item, a date I set myself is never moved by a script, and nothing gets closed without proof.

![How havoc works: Slack, GitLab, Todoist and my Claude sessions feed one ledger, the board shows it, and a re-check loop goes back to the sources](/images/havoc-flow.svg)

## How things get in

For Slack, a Claude session runs four times a day with read-only access and decides which messages are real obligations. That needs a model, because no search query can tell whether "let me check" was a promise or just a polite way to end a conversation. GitLab reviews and my personal Todoist come in through plain scripts, since there is nothing to judge there. On my phone I still use Todoist's quick add, and those tasks land in an inbox on the board until I give them a project or a date, or drop them.

Every item gets a kind (something I promised, something asked of me, a question I never answered, something I am waiting on), a priority and a due date. Questions and requests get one working day by default, promises three. When the next move is someone else's, the item sits in a separate "waiting on them" block, and once its date passes, the row tells me to chase instead of turning red.

<!-- SCREENSHOT 2: a "no answer for N days, chase" row, or a drifting item -->

## Keeping the list honest

A list that only grows is a list you stop reading, so havoc keeps checking itself.

After every sweep, a second run takes fifteen open items and looks for proof that they were settled: the thread itself, other Slack channels, my sent mail and my calendar. If I answered a question by mail, the item closes and records where. If it finds nothing, the item stays open. An item that is closed by mistake is one I will never see again, so I would rather have it nag me once too often.

My own clicks get the same treatment. When I press Done, the item is first checked against the source before it really closes. It feels a bit strict, but it keeps me honest.

After each sweep, and once more in the evening, a session goes through the items an agent can handle and drafts the next step, mostly replies and chases in my words. The next morning they are waiting at the top of the board with Approve, Edit and Reject. Approve does not send anything; I still copy and post every message myself. When I reject a draft I have to say why, and those notes are what I use to improve the prompt.

<!-- SCREENSHOT 3: a proposal with its draft and the Approve, Edited, Reject buttons -->

![Four feedback loops around the ledger, from the Todoist sync every ten minutes to the weekly review of the drafts](/images/havoc-loops.svg)

There is also one rule for my own procrastination. Every time I move a date, the ledger counts it. After two moves the item gets a "drifting" label, and the third time I have to choose: write down a concrete next step, hand it to someone, or drop it.

## What I learned

- A model is good at deciding whether a sentence is a promise, and I did not need it for anything else. Dates, duplicates and notifications are plain code, which makes them a lot easier to debug.
- People do not always tag you. A colleague once made me owner of a redesign in a thread I had replied in, without an @, and the sweep missed it completely. It now reads every thread I posted in to the end.
- One copy of a list is enough. An earlier version mirrored everything to Notion, and a task I closed by hand would sit on the board for hours. The board now reads the ledger file directly.

The repository is private, because half of it is wired into my work tools. If you are building something similar, or you think a ledger is just a to-do list with ambition, [send me a mail](mailto:missions@tdlx.nl).
