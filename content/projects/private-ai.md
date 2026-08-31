---
title: Private AI Stack
date: 2026-05-01
draft: false
weight: 6
summary: 'A fully private ChatGPT alternative running at home: local models, private web search, zero data leaving the house.'
tech: ['Ollama', 'Open WebUI', 'SearxNG', 'Docker Compose']
---

## What it is
A self-hosted AI assistant for the household: Open WebUI as the chat interface, Ollama running local models on my own hardware, and SearxNG providing private, aggregated web search the models can use. It behaves like a hosted AI chat service, except no prompt, document or search query ever leaves the house.

## How it works
- Ollama serves quantised open models locally.
- Open WebUI provides the chat interface, user accounts and conversation history.
- SearxNG gives the assistant web search without handing queries to a single search provider.
- The whole stack is a Docker Compose file on the home server, behind the same reverse proxy and auth as everything else.

## Why
Partly practicality, because some questions and documents simply should not go to a third party, and partly to keep a hands-on feel for what local models can and cannot do, which feeds directly back into how I use AI in my professional work.
