# Context Packs And Token Usage

Agentify uses context packs to make project continuity automatic without pasting full chat history into every prompt.

## What Is A Context Pack?

A context pack is a bounded, auditable set of local context attached to a normal runner prompt.

It can include:

- durable user directives
- thread summary
- workspace summary
- recent relevant turns
- local memory search hits
- current runner state
- git status and repo graph facts
- browser fallback context
- the current user request

The coding agent sees enough context to continue work, while the user gets to ask naturally.

## Example

User writes:

```text
Continue the UI fix.
```

Agentify may silently add local context like:

```text
[source: user directive]
Build macOS arm64 only until told otherwise.

[source: local SQLite thread summary]
The active task is fixing command and assistant-code rendering in Agentify Chat.

[source: local git]
Several frontend and CLI files are modified.

[source: current request]
Continue the UI fix.
```

The user does not see this internal context pack as assistant output. It is there to help the runner answer correctly.

## Token Usage

Automatic context has a cost, but it should be bounded.

Agentify should prefer:

1. compact summaries before raw history
2. recent relevant turns before older turns
3. targeted memory hits before broad memory dumps
4. repo summaries before large file content
5. direct local answers when no model is needed

This means a real coding prompt may use a little extra context, but it should avoid the much more expensive failure mode: repeated "I do not know the previous context" turns.

## Zero-Token Local Answers

Some questions are local lookups and should not call a model:

```text
What was my last prompt?
What directives are saved?
What runners are active?
```

These can be answered from local SQLite with zero model tokens.

## Auditing

Context packs should be recorded locally with source labels and hashes. This lets users or developers inspect what context was included without storing plaintext in Agentify cloud.

Useful labels include:

- `user directive`
- `local SQLite thread summary`
- `local SQLite workspace summary`
- `recent local turn`
- `semantic memory`
- `keyword memory`
- `local git`
- `repo graph`
- `browser fallback`

## Practical Guidance

For users:

- Ask naturally for normal work.
- Use memory search when you want to inspect what the runner remembers.
- Use forget only when you intentionally want to remove local continuity.

For Agentify:

- Keep context packs bounded.
- Keep direct local answers cheap.
- Keep orchestration explicit.
- Keep cloud storage ciphertext-only for private chats.
