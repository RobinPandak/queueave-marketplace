# QueueAve matchmaking process

Goal: build fair, balanced matches. Read `leveling.md` first for the ladder and ELO.

## Data
Call `list_session_players(sessionId, sortBy: 'priority')`. Each player has: skill_level, level_rank (0-8), elo_rating/elo_singles/elo_doubles, games_played, waiting_minutes, currently_playing, priority_rank (1 = should play next), locked_partner_id, checked_in, status.

## Eligibility
- For a COURT match: eligible = `checked_in === true && currently_playing === false`. Never put a currently-playing player on a court.
- For an ON-DECK (queued) match: eligible = `checked_in === true`. Currently-playing players ARE allowed (they play later).
- Never use the same player twice in one match, and never across two simultaneous court matches.

## Priority (most important)
Fewest games played and longest wait come first. Trust `priority_rank` (it already blends games + wait via the app's fairness model). Break ties by higher `waiting_minutes`, then lower `games_played`.

## Format
- Doubles by default (2 vs 2 = 4 players). Singles (1 vs 1 = 2 players) when exactly two players are named, or doubles is not in the session's allowed formats.

## Selection + balance (default = balanced by level)
1. Take the highest-priority eligible players to fill the match (4 for doubles, 2 for singles).
2. Honor locked pairs: if a selected player has a `locked_partner_id` that is also eligible, keep them on the same team (pull the partner in if needed).
3. Split into two teams so the teams' average `level_rank` are as close as possible; break ties by closest average ELO (elo_doubles for doubles/mixed, elo_singles for singles, else elo_rating).
4. Respect explicit user constraints: "Rose against Josh" (those two, opposing, singles); "balanced beginner-intermediate pair" (compose teams from those levels); a named pairing stays together.

## Act
- `/match`: create on the next open court (a court 1..num_courts that is not closed and has no pending/in_progress match) via `create_match` (status pending, not started). If no court is open, `add_to_on_deck` instead and say so.
- `/queue`: `add_to_on_deck`. You may queue several at once.

## After
Report the lineup back by player names (and levels), the court (or on-deck), and a one-line why (priority + balance). Do not expose raw UUIDs to the user.
