# ScoreVault

**Private prediction leagues for your group chat.** Create a league, invite your friends, call the exact score of every match, and climb the leaderboard until the final whistle.

ScoreVault is a mobile-first web app built around one idea: the friends' pick'em pool you already run in a spreadsheet or a WhatsApp thread, made instant, automatic and a lot more fun. It launched for the 2026 FIFA World Cup.

## How it works

1. **Join a league.** Sign in with Google (or play as a guest), create a private league or join one with a code.
2. **Make your picks.** Enter the exact scoreline for each match, matchday by matchday.
3. **Picks lock at kick-off.** No edits once the match starts.
4. **Earn points.** An exact score earns the most points, weighted by rarity: calling the upset pays more than calling the favorite. A correct result earns a bonus.
5. **Win bragging rights.** Results sync automatically and the league leaderboard updates after every match.

## What's in the app

| Tab | What it does |
| --- | --- |
| **My Picks** | Match cards by matchday with points on offer for each outcome |
| **Results** | Final scores and how every pick scored |
| **Deposit** | League prize pool, coming soon |
| **My Leagues** | Leaderboards and league members |
| **Profile** | Stats, pick history, badges, friends and rules |

## Tech stack

- **App:** Next.js 16 (App Router), React 19, TypeScript, Tailwind CSS 4, Framer Motion
- **Backend:** Supabase (Postgres + Auth): profiles, leagues, league members, picks, results, waitlist
- **Data:** official scores synced from ESPN's public scoreboard; outcome probabilities from Polymarket drive the rarity-weighted points
- **Hosting:** Vercel
- **On-chain prize pool (prototype):** Solidity contracts on Base, built with Foundry. See [`contracts/`](contracts/)

```
app/
  page.tsx          the whole mobile app (screens, tabs, match cards)
  api/              results sync, admin results, waitlist, odds, testnet faucet
  lib/              Supabase client, match types, Polymarket helpers, contract ABIs
contracts/          league vault, picks, scoring, oracle and payout contracts + tests
docs/               original research brief (French)
```

## Run it locally

```bash
npm install
cp .env.example .env.local   # fill in your Supabase keys and an admin secret
npm run dev                  # open http://localhost:3000
```

## Roadmap

- One-tap league invites shared straight into WhatsApp and iMessage
- League prize pools with a transparent, non-custodial payout
- More competitions beyond the World Cup

## Team

Built by [Vianney Vendeuvre](https://github.com/Vianneyvdv) (product, design, growth) and Jules (engineering).
