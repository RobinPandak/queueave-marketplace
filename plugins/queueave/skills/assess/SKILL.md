---
name: assess
description: Assess a player's skill level and ELO from their match history and recommend an updated level. Use for "/assess", "/assess Josh", "assess all players in the current session", "is Maria still a beginner?". Recommends a level with reasoning; only applies it (which recomputes ELO) after you confirm.
---

# Assess a player's level (/assess)

Read `${CLAUDE_PLUGIN_ROOT}/reference/leveling.md` for the ladder and ELO.

For each target player:
1. Resolve the player id (`find_player` or from `list_session_players`). For "all players in the current session", iterate the session's checked-in players.
2. Pull `get_player_match_history(playerId, scope: 'global')`. Use the `summary` (wins, losses, win_rate, avg_opponent_elo, avg_teammate_elo, count) and scan `matches` for opponent/teammate levels.
3. Judge relative to others:
   - Strong, consistent wins against opponents at or above their level/ELO -> recommend a higher level.
   - Frequent losses against opponents at or below their level/ELO -> recommend a lower level.
   - Few matches (e.g. < 4) -> say the sample is small; recommend keeping the current level unless the signal is strong.
   - Map to the ladder: a player beating avg-opponent-ELO ~1300 consistently is around intermediate_b/a; ~1500+ is advanced; ~1000-1100 is beginner. Use level_rank distance, not just raw ELO.
4. Output: current level + ELO, recommended level, the win/loss record, avg opponent ELO/level, and a one-paragraph rationale citing specific results.
5. Ask whether to apply. Only on an explicit yes, call `set_player_level(playerId, recommendedLevel)` (this recomputes ELO from history with a floor at the new level's starting ELO and is not easily reversible). Confirm what changed.

Never auto-apply without confirmation. When assessing many players, present a table of recommendations first, then apply only those the user approves.
