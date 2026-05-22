# Privacy, Storage, And The Local Wall

Agentify Chat uses a local wall: private plaintext should stay on the paired computer and in the browser, not in Agentify cloud.

## Where Data Lives

| Data | Browser | Local runner | Agentify cloud |
| --- | --- | --- | --- |
| Browser UI cache | Yes | No | No |
| Workspace encryption material | Yes, as needed for paired browser | Yes | No |
| Private prompt plaintext | Yes | Yes | No |
| Private assistant plaintext | Yes | Yes | No |
| Runner SQLite continuity | No | Yes | No |
| Encrypted job envelopes | No | No | Yes |
| Realtime routing/status | Yes | Yes | Yes |
| Published snapshots | Yes | No | Yes, only after user publishes |

## What The Cloud Should See

For private chat jobs, Agentify cloud needs operational metadata:

- device and workspace identifiers
- encrypted job envelope
- timestamps and status
- ciphertext size
- runner presence
- routing target, when a runner is explicitly targeted

It should not need private prompt or response plaintext.

## Local Runner SQLite

The local runner SQLite journal is the continuity source of truth for a paired workspace. It can store:

- prompts and final answers
- summaries
- context-pack audit records
- memory search entries
- directives
- runner and orchestration state

This is local private state. Protect the machine and user account that runs the CLI.

## Browser Cache

Browser storage is a convenience cache. A browser may have a local view of chats, but it is not the authority for project continuity.

If a new browser pairs and has no local history, it can hydrate from the local runner through encrypted runner-mediated snapshots.

## Published Snapshots

Publishing is separate from private chat.

When you publish:

- you review a redacted snapshot
- the approved snapshot becomes public through a share link
- the owner token stays with the publishing browser
- unpublish/rename requires the owner token

Redaction is best effort. Review before sharing.

## Security Model In Plain Language

Agentify Chat does not make a local coding agent harmless. If you pair a folder and run a direct-write local runner, that runner has the same effective access as the CLI process on your machine.

Recommended usage:

- pair only trusted folders
- keep the runner visible while testing
- use review-only runners for parallel review
- reset pairing for browsers or machines you no longer trust
- keep the CLI updated
