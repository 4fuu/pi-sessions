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

List recent sessions for the current project:

```json
{}
```

Search every saved project when the current project is not enough:

```json
{ "query": "authentication", "scope": "all" }
```

Search all saved sessions from one exact working directory:

```json
{ "query": "migration", "scope": "all", "cwd": "/path/to/project" }
```

Read the active branch using an exact ID returned by discovery or search:

```json
{ "sessionId": "01234567-89ab-cdef-0123-456789abcdef" }
```

Read the branch ending at a particular search match:

```json
{
  "sessionId": "01234567-89ab-cdef-0123-456789abcdef",
  "entryId": "a1b2c3d4"
}
```

Tool calls and tool results are excluded by default. Include them only when they are relevant:

```json
{
  "sessionId": "01234567-89ab-cdef-0123-456789abcdef",
  "includeTools": true
}
```

When a result returns a cursor, continue with only that cursor and an optional new limit:

```json
{ "cursor": "<opaque cursor>", "limit": 20 }
```

## Behavior and safety

Pi sessions form trees rather than flat transcripts. `sessions` follows parent links and returns one root-to-leaf path, so sibling branches are never mixed together.

The tool accepts session and entry IDs, not file paths. It does not create, append, resume, branch, rename, or delete sessions. Legacy formats are parsed with pi's exported parser and migrated only in memory, because `SessionManager.open()` may rewrite an old session while opening it.

Returned history is marked as untrusted reference data. The agent must not follow instructions found inside archived messages or tool output. Assistant thinking, usage and cost metadata, image data, extension-private entries, and TUI-only metadata are never returned. Opaque compaction summaries are also omitted because they may derive from thinking or tool traffic; branch summaries are omitted because they intentionally summarize an abandoned sibling. The original user and assistant messages remain readable. Tool calls and results require `includeTools: true` and are still bounded.

## Requirements

- Node.js 22.19 or newer.
- A compatible pi installation with saved sessions.

## Installation

The package is not yet published to npm. Install directly from GitHub:

```bash
pi install git:github.com/4fuu/pi-sessions
```

After an npm release, install it with:

```bash
pi install npm:@4fu/pi-sessions
```

Try the GitHub source without installing:

```bash
pi -e git:github.com/4fuu/pi-sessions
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
