---
name: queue
description: Add one or more balanced matches to the on-deck queue (not a court) in the current QueueAve session. Use for "/queue", "queue the next match", "queue the next 3", "put X and Y on deck". Same balancing as /match but places matches on-deck; may include players who are currently playing since queued matches play later.
---

# Queue matches on-deck (/queue)

Read `${CLAUDE_PLUGIN_ROOT}/reference/matchmaking.md` and `${CLAUDE_PLUGIN_ROOT}/reference/leveling.md` first.

Same matchmaking as the match skill, with two differences:
- Eligibility is `checked_in === true` (currently-playing players ARE allowed on-deck).
- Place via `add_to_on_deck(sessionId, team1, team2, format)` instead of create_match. There is no court limit; you can queue any number.

Process:
1. Resolve the session (from /operate context or `list_sessions`).
2. `list_session_players(sessionId, sortBy: 'priority')`.
3. If asked for N matches, compose N balanced lineups in priority order, not reusing a player within the same on-deck batch unless the user says it's fine.
4. Call `add_to_on_deck` for each. Report each queued lineup by names + levels.
