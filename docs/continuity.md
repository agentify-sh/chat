# Continuity And Local Memory

Agentify Chat is designed so a project can continue from any paired browser while the private project context stays on the paired computer.

## What This Means For Users

If you pair a new browser, clear browser storage, or come back hours later, Agentify should still know the project state that the local runner recorded.

Example:

```text
User: What were we doing?
Agentify: We were fixing the setup sidebar behavior. The last known blocker was...
```

That answer should come from the local runner's SQLite journal, not from the new browser's local cache.

## Source Of Truth

Agentify uses three different places for state:

- Browser storage: fast local UI cache for the browser you are using.
- Local runner SQLite: the authoritative private continuity record for the paired workspace.
- Agentify cloud: encrypted routing and transient status only.

The cloud should not need plaintext chat history to help you continue. The local runner sends encrypted history snapshots to the browser when needed.

## What Gets Remembered Locally

The local runner records operational memory such as:

- recent user prompts and assistant responses
- thread summaries
- workspace summaries
- user directives, such as "going forward, build mac arm64 only"
- context-pack sources used for each turn
- runner state, such as mode and active runner handle
- local memory search entries
- orchestration parent and child run records

This is not meant to be a giant raw transcript dump. Summaries are used so the agent can continue work without resending every old message.

## Automatic Hydration

When a browser pairs or reconnects, Agentify can request a runner history snapshot. The local runner builds a bounded snapshot with recent threads, summaries, active runners, and memory status. The browser shows reconstructed thread entries so a clean browser is not empty.

Hydrated threads should be visually marked as reconstructed from local runner memory.

## Direct Local Answers

Some questions do not need a model call:

```text
What was my last prompt?
What was the previous response?
What workspace is this?
What directives are saved?
What runners are active?
```

These can be answered directly from local SQLite. That saves tokens and avoids giving a model a task that is really a local lookup.

## Forgetting Memory

There are different kinds of deletion:

- Browser-local delete removes the current browser's cached view.
- Runner-side forget removes local runner continuity for a thread or workspace.
- Published snapshot deletion/unpublish affects public shared snapshots.
- Pairing reset revokes browser or runner trust.

Runner-side forget should be explicit because it removes useful local continuity.
