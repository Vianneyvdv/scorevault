# Data model

The tables the app needs on Bilt's built-in backend. You don't paste this file into Bilt in one go: each build step in `build-steps.md` asks for the tables it needs. Keep it open as the reference to check against (Bilt → Settings → Database → Data).

## Tables

**profiles**: one row per signed-in user
| Field | Type | Notes |
| --- | --- | --- |
| id | user id | the signed-in user |
| display_name | text | shown everywhere |
| avatar | text | an emoji or an image |
| created_at | timestamp | |

**games**: one row per real game
| Field | Type | Notes |
| --- | --- | --- |
| id | text | the ESPN event id |
| sport | text | `nba` for the MVP |
| starts_at | timestamp | tip-off; picks lock at this time |
| home_team, away_team | text | full team names |
| home_abbr, away_abbr | text | e.g. BOS, NYK |
| home_price, away_price | number 0–1 | the market probability that each side wins |
| status | text | `scheduled`, `live` or `final` |
| home_score, away_score | number | filled when live or final |
| winner | text | `home` or `away` when final |
| featured | boolean | true for the day's Daily 3; chosen once per day, then frozen |

**picks**: one row per user per game
| Field | Type | Notes |
| --- | --- | --- |
| id | id | |
| user_id | user id | |
| game_id | text | → games.id |
| side | text | `home` or `away` |
| price | number 0–1 | the side's price **at the moment of the pick**, frozen |
| created_at | timestamp | |
| result | text | `pending`, `won` or `lost` |
| pnl | number | in units; null while pending |

One pick per user per game. A pick can change until tip-off, then it is locked.

**leagues**
| Field | Type | Notes |
| --- | --- | --- |
| id | id | |
| name | text | |
| invite_code | text | 6 characters, unique |
| created_by | user id | |
| created_at | timestamp | |

**league_members**
| Field | Type | Notes |
| --- | --- | --- |
| league_id | id | → leagues.id |
| user_id | user id | |
| joined_at | timestamp | |

## Rules that matter

- **P&L of a pick** (1 unit staked): won → `1 / price − 1`, lost → `−1`. Example: a pick at 0.25 pays +3.00; a pick at 0.80 pays +0.25.
- **Who can see what:** a user sees their own picks and profile, plus the picks and profile (name, avatar) of anyone who shares a league with them. Any signed-in user can look up a league by its exact invite code, to join it. Nothing else is visible.
- **Prices come from the server**, never from the phone: when a pick is saved, the server copies the side's current price from the games table. Picks lock at tip-off, enforced on the server.
- **Streak** = consecutive days, up to today, on which the user picked all of that day's featured games. Computed from picks, no extra table.
