# Runner Modes And Orchestration

Agentify Chat can run a coding agent in fresh mode, persistent mode, or explicit multi-runner orchestration. The goal is the same in all cases: the user asks naturally, and Agentify supplies the right local context automatically.

## Fresh Mode

Fresh mode starts a new one-shot runner turn for each prompt.

High-level flow:

1. Browser sends an encrypted prompt.
2. Local runner decrypts it.
3. Local runner builds a bounded context pack from SQLite and repo state.
4. The coding agent receives the current request plus that context.
5. The final answer updates local summaries and memory.

User experience:

```text
User: Continue fixing the setup sidebar bug.
Agentify: I found the issue in the responsive setup panel state...
```

The user should not need to paste old chat history into the prompt. Fresh mode gets the project context before each run.

## Persistent Mode

Persistent mode keeps a long-lived terminal session attached to the coding agent.

The important rule is that persistent mode should not rely only on the terminal's memory. Before each normal turn, Agentify still builds a current context pack and injects it with the request.

User experience:

```text
User: What changed since the last browser session?
Agentify: The runner has local summaries showing...
```

This helps after reconnects, browser changes, or long gaps. The browser should show the answer, not internal context refresh text or terminal status noise.

## Orchestration

Orchestration is explicit because it can multiply cost and create write conflicts.

Examples:

```text
/review Check this patch for release risk.
/race Propose the safest fix.
/all Compare approaches.
/handoff @codex_12345 @reviewer_23456 Continue the release review.
@reviewer_23456 Inspect the current plan.
```

High-level flow:

1. Browser creates child jobs for the selected runners.
2. Each child job is encrypted and routed to a target runner handle.
3. Each runner builds its own scoped local context pack.
4. The UI labels and groups outputs by runner.
5. Local runner SQLite records orchestration parent and child rows.

## Write Safety

Only one direct-write runner should be active for a workspace by default. Other runners should be review-only unless isolated worktree support is explicitly enabled.

This avoids silent conflicts where multiple agents edit the same project at the same time.

## Why Orchestration Is Not Automatic

Normal continuity should be automatic. Multi-runner orchestration should not be automatic because it affects:

- cost
- rate limits
- write safety
- attribution
- review quality

The user should choose when to fan out work.
