# ScoreVault on Bilt — build kit

The mobile app is built on [Bilt](https://bilt.me) (React Native / Expo), using Bilt's built-in backend. This folder holds everything to paste into Bilt, in order. The Next.js app at the root of this repo is the earlier World Cup web version; the mobile app is a fresh build.

**The product in one line:** a social picks app where every pick has a price (the probability it wins) and every player has a P&L. Friends' leagues for the game, a trading-app feel for the experience. NBA only for the first version, free, no money.

## Files

| File | What it's for |
| --- | --- |
| [`01-first-build.md`](01-first-build.md) | The first prompt: app shell + one complete journey on sample data |
| [`build-steps.md`](build-steps.md) | Steps 2–12, one prompt each: sign-in, games and picks, real games, prices, results, leagues, leaderboard, feed, profile, share card, demo league. Plus fix templates and how to test without live games |
| [`data-model.md`](data-model.md) | The tables and rules, to check what Bilt builds |
| [`data-sources.md`](data-sources.md) | ESPN for games and scores, The Odds API for prices, how the server syncs |

## Before the hackathon

- [ ] Create a Bilt account (app.bilt.me) and check how many tokens your plan includes
- [ ] Sign up at the-odds-api.com (free) and keep the API key somewhere safe
- [ ] Line up 3–4 people (teammates, friends) to join your league on the first evening and pick that night's games, so the demo has real picks
- [ ] Read through `01-first-build.md` and replace the app name if it's chosen
- [ ] In Bilt, once the project exists: Settings → GitHub → connect your account and sync the project to a new repo under Vianneyvdv

## On the day

1. Paste `01-first-build.md`, check its "Done when" points in the live preview
2. Send the steps in `build-steps.md` one by one, testing after each
3. Steps 2 → 8 = minimum demo; 9 and 10 complete the demo flow below; 2b, 11 and 12 are optional
4. Something breaks → the bug template at the end of `build-steps.md`; never stack a new feature on a broken one

## Demo flow (2 minutes)

1. Sign in, pick a name and an emoji
2. Tonight: the Daily 3, real NBA games, a price on each side → make 3 picks, show what each pays
3. Leagues: create "Hackathon crew", share the code, a teammate joins on their phone
4. Leaderboard: the league ranked by P&L
5. Feed: the teammate's pick appears, "Against the crowd" tag on an upset pick
6. Profile: the P&L curve and the stats — "every player has a P&L"
