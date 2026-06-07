# QueueAve plugin

Operate [QueueAve](https://badminton.queueave.com) badminton sessions from Claude. The plugin bundles:

- The **`queueave` MCP server** (`.mcp.json`), an HTTP MCP server hosted at `https://badminton.queueave.com/api/mcp` with tools for sessions, matches, players, courts, walk-ins, payments, match history, and linked pairs. It uses OAuth (Google sign-in), no personal token required.
- The **`/queueave:operate` skill**, an operating manual that teaches Claude the tools, the find-session then act workflow, and the important side effects.
- The **`/queueave:match` skill**, which builds and places a level-and-ELO balanced court match from the priority queue.
- The **`/queueave:queue` skill**, which adds one or more balanced matches to the on-deck queue.
- The **`/queueave:assess` skill**, which reviews a player's match history and recommends a level change.

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

- "/operate Evening Rally" (load session context and summary)
- "/match" (balanced match on the next open court)
- "/match Rose against Josh" (specific players)
- "/queue the next 3" (add 3 on-deck matches)
- "/assess Josh" (review match history and recommend a level)
- "Register walk-in Maria Cruz as intermediate_d"
- "Mark Josh paid"
- "Link Ana and Ben" (locked pair, always same team)
- "List my QueueAve sessions"
- "Show the players in Tara Court Season 3"
- "Create a doubles match on court 2 with Alice and Bob versus Carol and Dan, and start it"
- "End match X 21 to 15, team 1 won"
- "Drain the queue onto open courts"
- "Close court 3"

Every action is scoped to your own sessions.

## Skills

| Command | What it does |
| --- | --- |
| `/queueave:operate` | Operating manual: connect, find sessions, act. Run `/operate <name>` to load a session by name. |
| `/queueave:match` | Build a balanced court match from the priority queue. |
| `/queueave:queue` | Add one or more balanced matches to the on-deck queue. |
| `/queueave:assess` | Review a player's match history and recommend a level change. |

## Tools

| Group | Tools |
| --- | --- |
| Read | `list_sessions`, `get_session`, `list_session_players`, `list_matches`, `get_session_match_history`, `get_player_match_history`, `find_player` |
| Sessions | `create_session`, `update_session_config`, `set_session_status`, `set_on_deck_buffer` |
| Players | `register_walkin`, `set_player_level`, `set_player_check_in`, `add_player_to_session`, `set_player_status`, `set_player_payment`, `link_players`, `unlink_players` |
| Matches | `create_match`, `start_match`, `end_match`, `cancel_match`, `add_to_on_deck`, `promote_to_court`, `drain_queue`, `remove_from_on_deck` |
| Courts | `add_court`, `remove_court`, `set_num_courts`, `set_court_status`, `rename_court` |

## Security

- Authorization uses OAuth 2.1 (Supabase) with your Google account; access is consent-based and revocable from your Supabase account, and tokens are short-lived.
- The server uses the Supabase service role internally but enforces that every action belongs to the token's organizer.
- Access is consent-based; you can revoke it at any time from your Supabase account.
