# Agentify Chat Docs

Agentify Chat is a browser UI for working with coding agents that run on your own computer. The browser is the control surface. The local Agentify CLI is the execution and plaintext boundary.

This repository is intentionally source-less. It is for public documentation, issues, discussions, and support notes.

## Start Here

- [Continuity and local memory](./continuity.md): how a new browser can continue work without having the old browser's chat history.
- [Runner modes and orchestration](./runner-modes-and-orchestration.md): how fresh mode, persistent mode, and multi-runner flows behave.
- [Context packs and token usage](./context-packs-and-token-usage.md): why Agentify sends context automatically and how it stays bounded.
- [Slash commands and natural-language routing](./commands-and-routing.md): which actions are automatic and which commands are explicit controls.
- [Privacy and storage](./privacy-and-storage.md): what lives in the browser, local runner, and Agentify cloud.

## Product Principle

Normal users should be able to ask natural questions:

```text
What were we doing?
Continue fixing the setup sidebar.
What was my last prompt?
Search memory for the release issue.
```

Slash commands exist as advanced controls and testable shortcuts. They should not be required for ordinary continuity.
