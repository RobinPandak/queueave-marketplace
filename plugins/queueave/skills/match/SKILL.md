---
name: match
description: Create a balanced badminton match in the current QueueAve session and put it on an open court. Use for "/match", "match the next group", "Rose against Josh", "balanced beginner-intermediate pair", "make a doubles match on court 2". Builds level-and-ELO balanced teams prioritizing players with the fewest games and longest wait.
---

# Create a match (/match)

Read `${CLAUDE_PLUGIN_ROOT}/reference/matchmaking.md` and `${CLAUDE_PLUGIN_ROOT}/reference/leveling.md` before composing.

1. Determine the session: use the one from the current /operate context. If unknown, ask or call `list_sessions` (status in_progress).
2. Pull `list_session_players(sessionId, sortBy: 'priority')` and `get_session(sessionId)` (for num_courts, closed_courts, allowed_formats).
3. Compose the match per the matchmaking process: pick highest-priority COURT-eligible players (checked_in and not currently_playing), default balanced doubles, honor locked pairs, apply any constraints in the user's message.
4. Find the next open court: a number in 1..num_courts not in closed_courts and with no pending/in_progress match (check `list_matches(sessionId)` or `get_session` counts). 
5. Create it: `create_match(sessionId, courtNumber, team1, team2, format, start: false)`. If no court is open, `add_to_on_deck(...)` instead and tell the user it was queued.
6. Report the lineup by names + levels, the court, and a one-line rationale.

Auto-create (no confirmation step), but always echo what you created. If the user named specific players, use exactly those.
