# Data sources

Two external feeds keep the app live: the games and scores, and the price of each side. Both are called **from the server** (a Bilt automation), never from the phone.

## 1. Games and scores: ESPN scoreboard (free, no key)

```
https://site.api.espn.com/apis/site/v2/sports/basketball/nba/scoreboard?dates=YYYYMMDD
```

What the app reads in each item of `events[]`:

| Need | Field |
| --- | --- |
| Game id | `events[].id` |
| Tip-off time | `events[].date` |
| Teams | `competitions[0].competitors[]` → `homeAway` (`home`/`away`), `team.displayName`, `team.abbreviation` |
| Score | `competitors[].score` |
| Status | `competitions[0].status.type.state`: `pre` = scheduled, `in` = live, `post` = final |

The current ScoreVault repo already uses this same feed for soccer (`app/api/sync-scores/route.ts`).

**Caveat:** this is ESPN's public but undocumented API. It has worked for years, but it can change without notice. Fallback for the demo: sample games (see the end of this file).

## 2. Prices: The Odds API (free plan, key needed)

```
https://api.the-odds-api.com/v4/sports/basketball_nba/odds?regions=us&markets=h2h&oddsFormat=decimal&apiKey=KEY
```

- Free plan: 500 credits a month; one call for one market in one region costs 1 credit. The app calls it at most every 6 hours (~120 credits a month).
- Sign up at the-odds-api.com before the hackathon and keep the key ready. **The key goes in Bilt's server secrets, never in a prompt or in the app.**
- Check the sport key `basketball_nba` in their docs on sign-up.

**From odds to price:** for each game, take the decimal odds of each side (one bookmaker, or the average), then:

```
raw_home = 1 / home_odds        raw_away = 1 / away_odds
home_price = raw_home / (raw_home + raw_away)
away_price = raw_away / (raw_home + raw_away)
```

The division removes the bookmaker's margin, so the two prices add up to 1.

**Matching ESPN games to Odds API games:** same teams, tip-off within 12 hours (the Odds API gives times in UTC, so a 10 PM Eastern game is already the next day there). Team names can differ slightly ("LA Clippers" vs "Los Angeles Clippers"), so compare simplified names or keep an alias list. Find each team in the outcomes by name, never by position.

**Never in the UI:** the words odds, bet, or bookmaker names. These stay internal; the app only says "price".

**Fallback if there are no prices:** 0.50 / 0.50 and a "price pending" label. The game can still be picked.

## When the server runs

Bilt doesn't document scheduled jobs. The build steps therefore use **sync on open**: when someone opens Tonight, the app calls a server automation that:

1. Fetches today's and yesterday's games from ESPN (yesterday so that late games, which end after midnight, get their final score), inserts new ones, refreshes scores and status
2. Fetches prices from The Odds API, at most every 6 hours, for games that haven't started
3. Settles every pending pick on a final game (result + P&L)
4. Chooses the Daily 3 once per day, then freezes it: the 3 games closest to 50/50 among games not yet started (the first 3 by tip-off if prices aren't available; all of them if fewer than 3)

If a scheduled automation turns out to be available in Bilt, run the same logic every 15 minutes instead.

## Sample data for the first build

The first build uses sample data, clearly labelled as such, before the real feeds are wired in:

| Game | Home price | Away price |
| --- | --- | --- |
| Boston Celtics vs New York Knicks | 0.62 | 0.38 |
| Denver Nuggets vs Los Angeles Lakers | 0.55 | 0.45 |
| Golden State Warriors vs Oklahoma City Thunder | 0.41 | 0.59 |
