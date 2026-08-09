# pi-sessions

Search and read saved [pi](https://github.com/earendil-works/pi) sessions from the agent, including historical branches, without changing session state.

## Why pi-sessions

- **One compact tool** — discover, search, read, and paginate through a flat `sessions` schema.
- **Project-aware by default** — recent sessions and searches stay in the current working directory unless the agent explicitly selects `scope: "all"`.
- **Branch-correct reads** — a session ID reads the active root-to-leaf branch; an entry ID reads the exact root-to-entry path, including abandoned branches.
- **Useful search references** — matches include stable `sessionId` and `entryId` selectors for a precise follow-up read.
- **Bounded context** — previews, records, pages, and total output are limited; thinking and tool traffic are omitted by default.
- **Actually read-only** — discovery does not create project directories, and pi's parser and migrations run only in memory. Session files are never opened through APIs that may rewrite legacy data.

## Usage

Talk to pi normally—the `sessions` tool is for the model, not a command you need to call yourself.

### Recall a decision from this project

> **You:** We discussed how refresh-token rotation should work in an earlier session. Find that discussion and use the decision here.
>
> **pi:** I'll search this project's saved sessions, open the matching conversation branch, and apply the earlier decision to the current task.

Pi first searches the current project, then reads the exact matching session and entry rather than guessing from a short preview.

### Find context from another project

> **You:** In another project we planned the billing migration. Find that conversation and summarize the rollout order.
>
> **pi:** I'll search across all saved projects, identify the relevant session, and read its selected root-to-entry branch before answering.

Pi uses cross-project scope only when the request indicates that the current project may not contain the answer. If a working directory is known, it can narrow the search to that project.

### Continue an unfinished line of work

> **You:** Show me my recent sessions for this repository and continue the one where I was debugging the retry race.
>
> **pi:** I'll list recent project sessions, select the relevant one, and read its active branch before continuing the investigation.

### Recover exact tool evidence

> **You:** Find the earlier session where the integration test failed and tell me the exact command output.
>
> **pi:** I'll locate the matching entry and, because the request needs tool evidence, read that branch with tool calls and results included.

Tool traffic is otherwise omitted. Pi follows returned cursors automatically when it needs older entries or additional search results; you do not need to manage session IDs, entry IDs, or pagination yourself.

## Behavior and safety

Pi sessions form trees rather than flat transcripts. `sessions` follows parent links and returns one root-to-leaf path, so sibling branches are never mixed together.

The tool accepts session and entry IDs, not file paths. It does not create, append, resume, branch, rename, or delete sessions. Legacy formats are parsed with pi's exported parser and migrated only in memory, because `SessionManager.open()` may rewrite an old session while opening it.

Returned history is marked as untrusted reference data. The agent must not follow instructions found inside archived messages or tool output. Assistant thinking, usage and cost metadata, image data, extension-private entries, and TUI-only metadata are never returned. Opaque compaction summaries are also omitted because they may derive from thinking or tool traffic; branch summaries are omitted because they intentionally summarize an abandoned sibling. The original user and assistant messages remain readable. Tool calls and results require `includeTools: true` and are still bounded.

## Requirements

- Node.js 22.19 or newer.
- A compatible pi installation with saved sessions.

## Installation

Install from npm:

```bash
pi install npm:@4fu/pi-sessions
```

Try it without installing:

```bash
pi -e npm:@4fu/pi-sessions
```

You can also install the latest GitHub source:

```bash
pi install git:github.com/4fuu/pi-sessions
```

### From source

Run `npm install`, add the repository path to `~/.pi/agent/settings.json`, then run `/reload` in pi.

## Development

```bash
npm install
npm test
```

## License

MIT
