# Agentify Chat

Agentify Chat lets you talk to a local coding agent from the browser while your computer runs the local Agentify CLI in the folder you paired.

Use the app at <https://chat.agentify.sh>.

Install the CLI from npm: <https://www.npmjs.com/package/@agentify/cli>.

This repository is the public issue and discussion tracker for Agentify Chat. The open source code release is coming. For now, this repository intentionally does not contain backend code or implementation source.

## The local wall

Agentify Chat is built around a local wall: the server routes encrypted work, but the paired computer is the place where repo access, runner execution, decryption, and long-lived local state live.

That means:

- Your paired computer creates and stores the workspace key during pairing.
- The server receives ciphertext, routing metadata, timestamps, and status transitions.
- The server does not receive the workspace key, local poll token, repo files, or private chat plaintext.
- The local CLI decrypts prompts, runs the selected coding agent in the paired folder, and encrypts responses back to the browser.
- Private chat history is stored in the browser and local runner state, not as readable server-side chat logs.

Direct mode is intentionally powerful. Remote prompts run through the selected local CLI with that CLI's normal file, command, and network permissions in the paired folder. Only pair machines, folders, and agents you trust.

## Architecture

A typical private chat turn looks like this:

1. The browser creates a local browser signing key.
2. The server issues a short-lived pairing ticket after a human check.
3. The Agentify CLI pairs a local folder with that ticket.
4. The local CLI creates the workspace encryption material and stores the poll token locally.
5. The browser queues an encrypted prompt, signed by the browser key and protected with a replay nonce.
6. The Cloudflare Worker stores only the encrypted job envelope and wakes the paired runner through a Durable Object WebSocket.
7. The local runner claims the job, decrypts it locally, runs the selected coding agent, and streams encrypted status/output chunks back.
8. The browser decrypts the response and stores the chat locally.

The routing layer is designed so the server can coordinate delivery without reading private prompt or response bodies.

## Encryption and ciphertext

Private chat payloads are encrypted before they leave the browser or paired computer. The server stores and relays ciphertext plus operational metadata needed to route and expire jobs.

The server can see:

- device and workspace identifiers
- request timing and status
- ciphertext sizes
- runner connection state
- public published snapshots you explicitly publish
- basic abuse-prevention metadata such as Turnstile and request metadata

The server should not be able to see:

- private prompt plaintext
- private assistant response plaintext
- the workspace encryption key
- the local poll token kept on the paired computer
- files from the paired folder unless you explicitly publish text that contains them

Browser signing is separate from encryption. Encryption keeps private message bodies unreadable to the server. Browser signing proves that queue, publish, rename, and unpublish actions came from a browser that was paired for that workspace, and replay nonces prevent reusing old signed requests.

## Published chats

Private chats stay local unless you choose to publish a redacted snapshot.

Publishing creates a share link for the snapshot you reviewed. The public share link is a viewer link, not an owner credential. Rename and unpublish actions require an author token that is returned only to the publishing browser and stored locally there.

Important details:

- Publishing uploads the redacted snapshot content you approved.
- The public viewer endpoint does not return the author token.
- A viewer with only the share URL should not be able to rename, unpublish, or take over the published chat.
- Redaction is best effort. Review the preview before publishing.
- Unpublishing removes the public viewer route for that snapshot.

If you believe a published chat can be modified or unpublished by a non-owner, please open a security issue with reproduction details and do not share sensitive data publicly.

## Cost and abuse controls

The app is designed to avoid always-on polling and unbounded queues:

- Durable Object WebSockets are the realtime path for runner wakeups.
- Only one active runner session is allowed for a paired device at a time.
- Only one open job is allowed per paired device at a time in the current beta.
- Pairing tickets and browser approval windows are short lived.
- Jobs, messages, and published snapshots have size limits.
- Ticket creation is protected by a human check.
- Stale queue, nonce, and security-event data is cleaned up on a schedule.

These controls are part cost control and part safety control. They reduce stale storage growth, duplicate runners, replay attempts, and runaway job queues.

## Security posture

Agentify Chat is local-first, but it is not a sandbox. The local runner has the same effective access as the CLI you run on your machine. Treat pairing like granting remote control to that local agent in that folder.

Recommended usage:

- Pair only trusted machines and folders.
- Keep the runner visible while testing new workflows.
- Review published snapshots carefully before sharing links.
- Reset pairing from Setup if a browser or machine should no longer be trusted.
- Keep the CLI updated when the app asks for a newer runner.

## Issues and discussions

Use this repository for:

- bug reports
- setup issues
- security concerns that do not include private secrets
- feature requests
- product discussions

For private security reports, contact: team@agentify.sh.
