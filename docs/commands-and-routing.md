# Commands And Routing

Agentify Chat should work with natural language first. Slash commands are advanced controls, shortcuts, and testable escape hatches.

## Automatic Routing

Users should be able to ask:

```text
What were we doing?
Continue the work.
What was my last prompt?
Search memory for the auth issue.
```

Agentify decides whether to:

- answer locally from SQLite
- run the selected coding agent with a context pack
- refresh persistent context
- route an explicit orchestration request

## Direct Local Answer Examples

These should be answered locally when possible:

```text
What was my last prompt?
What was the previous response?
What workspace is this?
What model are we using?
What directives are saved?
What runners are active?
```

Why: these are deterministic continuity questions. A model call is unnecessary.

## Runner-With-Context Examples

These should go to the runner with an automatic context pack:

```text
Continue implementing the setup fix.
Explain the current failure.
Patch the renderer bug.
Review the latest changes.
```

Why: these require reasoning or project work.

## Explicit Commands

These commands are useful for power users and support:

```text
/threads
/memory <query>
/context
/refresh
/summary
/directives
/runners
/forget
```

They should not be required for ordinary usage.

## Explicit Orchestration Commands

These commands intentionally require user action:

```text
/review <prompt>
/race <prompt>
/all <prompt>
/handoff @source @target <prompt>
@runner_handle <prompt>
```

Why they are explicit:

- they can create multiple jobs
- they can increase cost
- they need clear runner attribution
- they can affect write safety

## Forget Is Special

Forgetting runner memory should be explicit. It deletes local continuity, so Agentify should not infer it from vague natural language.

Use cases:

```text
/forget
/forget thread <thread_id>
/forget workspace
```

Browser-local delete and runner-side forget are different operations.
