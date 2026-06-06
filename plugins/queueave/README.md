# QueueAve plugin

Operate [QueueAve](https://badminton.queueave.com) badminton sessions from Claude. The plugin bundles:

- The **`queueave` MCP server** (`.mcp.json`), an HTTP MCP server hosted at `https://badminton.queueave.com/api/mcp` with 22 tools for sessions, matches, players, and courts.
- The **`/queueave:operate` skill**, an operating manual that teaches Claude the tools, the find-session then act workflow, and the important side effects.

## Install

```
/plugin marketplace add RobinPandak/queueave-marketplace
/plugin install queueave@queueave
```

## Authenticate

The MCP server uses a personal token tied to your QueueAve organizer account.

1. Sign in at https://badminton.queueave.com (Google).
2. Open **Dashboard, Connect Claude** (`/dashboard/mcp`), click **Generate**, copy the token (`qa_mcp_...`).
3. Set it as an environment variable and restart Claude Code (or run `/reload-plugins`):

   ```bash
   export QUEUEAVE_MCP_TOKEN=qa_mcp_xxx        # macOS / Linux
   ```
   ```powershell
   setx QUEUEAVE_MCP_TOKEN "qa_mcp_xxx"        # Windows, then open a new terminal
   ```

Claude Code expands `${QUEUEAVE_MCP_TOKEN}` into the server's `Authorization: Bearer` header. A 401 means the token is missing, wrong, or revoked.

## Use

Ask Claude things like:

- "List my QueueAve sessions"
- "Show the players in Tara Court Season 3"
- "Create a doubles match on court 2 with Alice and Bob versus Carol and Dan, and start it"
- "End match X 21 to 15, team 1 won"
- "Queue the next match" / "Drain the queue onto open courts"
- "Close court 3" / "Set Grant to advanced"

Every action is scoped to your own sessions.

## Tools

| Group | Tools |
| --- | --- |
| Read | `list_sessions`, `get_session`, `list_session_players`, `list_matches`, `find_player` |
| Matches | `create_match`, `start_match`, `end_match`, `cancel_match`, `add_to_on_deck`, `promote_to_court`, `drain_queue`, `remove_from_on_deck` |
| Players | `set_player_level`, `set_player_check_in`, `add_player_to_session`, `set_player_status` |
| Courts / session | `set_court_status`, `rename_court`, `set_num_courts`, `set_session_status`, `set_on_deck_buffer` |

## Security

- The token is stored only as a sha-256 hash server side and is individually revocable.
- The server uses the Supabase service role internally but enforces that every action belongs to the token's organizer.
- Revoke a token from the same dashboard page; revoked tokens are rejected immediately.
