# QueueAve plugin

Operate [QueueAve](https://badminton.queueave.com) badminton sessions from Claude. The plugin bundles:

- The **`queueave` MCP server** (`.mcp.json`), an HTTP MCP server hosted at `https://badminton.queueave.com/api/mcp` with 22 tools for sessions, matches, players, and courts. It uses OAuth (Google sign-in) — no personal token required.
- The **`/queueave:operate` skill**, an operating manual that teaches Claude the tools, the find-session then act workflow, and the important side effects.

## Install

```
/plugin marketplace add RobinPandak/queueave-marketplace
/plugin install queueave@queueave
```

## Authenticate

The MCP server uses OAuth. There is no token to copy or manage.

**Claude Desktop / claude.ai (web):** Open Settings, then Connectors, then Add custom connector. Name it "QueueAve" and paste the URL:
```
https://badminton.queueave.com/api/mcp
```
Click Connect. A browser window opens to sign in with Google and approve access.

**Claude Code (CLI):**
```
claude mcp add --transport http queueave https://badminton.queueave.com/api/mcp
```
Claude Code opens the same browser sign-in on first use.

A 401 means your authorization expired or was revoked. Reconnect the connector (or re-run the sign-in) to get a fresh session.

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

- Authorization uses OAuth 2.1 (Supabase) with your Google account; access is consent-based and revocable from your Supabase account, and tokens are short-lived.
- The server uses the Supabase service role internally but enforces that every action belongs to the token's organizer.
- Access is consent-based; you can revoke it at any time from your Supabase account.
