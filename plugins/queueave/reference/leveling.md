# QueueAve leveling and ELO

Ladder, lowest to highest, with starting ELO and level_rank:

| rank | level | starting ELO |
| --- | --- | --- |
| 0 | newbie | 800 |
| 1 | beginner | 1000 |
| 2 | intermediate_f | 1200 |
| 3 | intermediate_e | 1200 |
| 4 | intermediate_d | 1200 |
| 5 | intermediate_c | 1200 |
| 6 | intermediate_b | 1200 |
| 7 | intermediate_a | 1200 |
| 8 | advanced | 1500 |

Notes:
- All six intermediate sub-tiers start at 1200 ELO. The letter is the differentiator: intermediate_a is the strongest intermediate, intermediate_f the weakest. Always use `level_rank` AND ELO together, never starting ELO alone.
- ELO is standard Elo, K=32. Three fields: `elo_rating` (overall, every match), `elo_singles`, `elo_doubles`. Use `elo_doubles` for doubles/mixed balancing, `elo_singles` for singles, falling back to `elo_rating` if a format field is missing.
- A team's strength is the mean of its members' ratings.
- Levels are set by assessment (`set_player_level`), which recomputes `elo_rating` from match history with a floor at the new level's starting ELO. ELO does not auto-promote a level.

Balancing guidance: aim for the two teams' average `level_rank` within ~1 and average ELO within ~100 when the pool allows.
