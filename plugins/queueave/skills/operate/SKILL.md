---
name: operate
description: Operate a QueueAve badminton session from Claude. Use when the user wants to view or manage QueueAve sessions, players, matches, the on-deck queue, courts, check-ins, player levels, or session status. Examples, "list my QueueAve sessions", "create a doubles match on court 2", "end the match 21-15", "check in Alice", "queue the next match", "close court 3", "set Grant to advanced". Requires the queueave MCP server (bundled with this plugin) and a personal token.
---

# Operating QueueAve via MCP

This plugin connects Claude to QueueAve (a badminton queue management app) through the `queueave` MCP server. Every tool acts only on the signed-in organizer's own sessions.

## Setup (one time)

The MCP server authenticates with a personal token tied to your QueueAve organizer account.

1. Sign in at https://badminton.queueave.com with Google.
2. Open Dashboard, then Connect Claude (`/dashboard/mcp`). Click Generate and copy the token (shown once). It looks like `qa_mcp_...`.
3. Set it in your environment as `QUEUEAVE_MCP_TOKEN`, then restart Claude Code (or run `/reload-plugins`):
   - macOS/Linux: `export QUEUEAVE_MCP_TOKEN=qa_mcp_xxx` (add to your shell profile to persist)
   - Windows PowerShell: `setx QUEUEAVE_MCP_TOKEN "qa_mcp_xxx"` then open a new terminal, or `$env:QUEUEAVE_MCP_TOKEN="qa_mcp_xxx"` for the current shell only

If a tool returns 401 / Unauthorized, the token is missing, mistyped, or revoked. Generate a new one and update the env var. You can revoke tokens anytime from the same dashboard page.

## Core workflow

Tools take UUIDs, not names, so almost every task starts by resolving ids:

1. `list_sessions` to find the session id (optional status filter: open, in_progress, completed).
2. `get_session(sessionId)` for courts, player and match counts, and settings.
3. `list_session_players(sessionId)` for each player's player_id, name, skill_level, elo, checked_in, games_played, and status.
4. Act using those ids.

Use `list_matches(sessionId, status?)` to see matches with teams resolved to names. Use `find_player(query)` to search players who appear in your sessions by name or email.

## Tool catalog

- Reads: `list_sessions`, `get_session`, `list_session_players`, `list_matches`, `find_player`
- Matches: `create_match`, `start_match`, `end_match`, `cancel_match`, `add_to_on_deck`, `promote_to_court`, `drain_queue`, `remove_from_on_deck`
- Players: `set_player_level`, `set_player_check_in`, `add_player_to_session`, `set_player_status`
- Courts and session: `set_court_status`, `rename_court`, `set_num_courts`, `set_session_status`, `set_on_deck_buffer`

## Rules and side effects

- Teams are arrays of player UUIDs. Doubles uses two ids per team, singles one. For `create_match`, all players must be checked in and not already in an active match.
- `create_match` takes a courtNumber (or null) and `start` (true begins immediately, false creates it pending). Format is one of singles, doubles, mixed.
- On-deck means queued matches. `add_to_on_deck` queues a match, `promote_to_court` moves a queued match onto a specific open court, and `drain_queue` auto-fills every open court from the queue in order.
- `end_match` applies ELO and credits games played. `winnerTeam` is 1, 2, or 0 for a draw. This is not easily reversible. Confirm the scores first.
- `set_player_level` recomputes the player's ELO from match history. Valid levels: newbie, beginner, intermediate_f, intermediate_e, intermediate_d, intermediate_c, intermediate_b, intermediate_a, advanced. Also not easily reversible.
- `set_session_status` to "completed" ends the session and starts the wrapped recap generation. It does not send the go-live email blast (use the app UI for that).
- `set_player_check_in` checking a player out reconciles any queued matches that included them.
- Everything is scoped to your organizer account. You cannot read or change another organizer's data (admins excepted).

## Working style

- Before any destructive action (`end_match`, `set_player_level`, `cancel_match`, `set_session_status` completed), confirm with the user and echo the names and scores you are about to apply.
- When the user refers to a player by name, resolve it to an id via `list_session_players` or `find_player`. If the name is ambiguous, ask which player they mean rather than guessing.
- Prefer reporting names back to the user, not raw UUIDs.
