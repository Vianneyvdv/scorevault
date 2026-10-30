# Steps 2 to 12 — Build prompts

One prompt per message, in this order. After each one, test the "Done when" points before sending the next. Bilt keeps the conversation's context, so each prompt only describes the change.

**How to test without live games:** the hackathon runs during the day, when no NBA game is on. To test locking, results and P&L, edit a game row in Bilt → Settings → Database → Data (change its tip-off time, status, scores), then reopen the screen.

**If you're short on time:** steps 2 → 8 give the minimum demo (sign in, real games with prices, picks, a league, a leaderboard). Steps 9 and 10 (feed, profile) complete the demo flow in `README.md`. 2b, 11 and 12 are optional.

---

## Step 2 — Backend and sign-in

```
Enable the backend. Add sign-in with email.

When a new user signs in for the first time, show a one-screen onboarding: pick a display name and an emoji avatar from a grid of 12 sports emojis. Save it in a profiles table (display name, avatar, created date).

Keep unchanged: the Tonight screen, the My picks view, the bottom navigation, the design.

Done when:
- Signing in with email works in the preview
- A first-time user sees the onboarding once, then lands on Tonight
- Their name and avatar show at the top of the Profile tab
- Signing out and back in keeps the same profile
```

## Step 2b (optional) — Google and Apple sign-in

```
Add Google and Apple sign-in next to email sign-in, on the same screen. Same onboarding for first-time users. Keep everything else unchanged.

Done when:
- The sign-in screen shows the three options
- Google sign-in works where the preview supports it
```

## Step 3 — Games and picks in the database

```
Move games and picks from the device to the database.

Create a games table: id, sport, tip-off time, home team, away team, home and away abbreviations, home price, away price (numbers between 0 and 1), status (scheduled, live or final), home score, away score, winner (home or away), featured (true or false). Fill it with the 3 sample games, featured = true.

Create a picks table: user, game id, side (home or away), price, created date, result (pending, won or lost), P&L in units.

Rules:
- One pick per user per game: switching team on a card updates that pick instead of creating a second one.
- The price of a pick is copied on the server from the game's current price for that side when the pick is saved. The app never sends the price.
- A pick can't be created or changed after the game's tip-off time. Enforce this on the server, not only in the app.
- Each user can only see and change their own picks for now.

Remove the two past sample picks from My picks. Tonight and My picks now read from the database.

Done when:
- Picks made on Tonight are still there after closing and reopening the app
- Switching team on a card updates the pick: the picks table (Settings → Database → Data) still has one row for that game
- After setting a sample game's tip-off time in the past in the database, its card shows "Locked" and the pick can't be changed
```

## Step 4 — Real NBA games

```
Replace the sample games with real NBA games. Delete the sample games and any picks on them.

Add a server automation called sync-games. It calls this public API (no key needed), once for today and once for yesterday, dates in Eastern time:
https://site.api.espn.com/apis/site/v2/sports/basketball/nba/scoreboard?dates=YYYYMMDD
For each item in events: the id is events[].id, the tip-off is events[].date, the teams are in competitions[0].competitors[] (homeAway is "home" or "away", team.displayName, team.abbreviation, score). The status comes from competitions[0].status.type.state: "pre" = scheduled, "in" = live, "post" = final. Insert new games, and update scores and status of existing ones.

Call sync-games every time someone opens the Tonight screen, at most once a minute.

The Daily 3 = 3 games of today marked featured. Choose them once per day, the first time today's games are synced, then don't change them that day. For now: the first 3 games of the day by tip-off time. If there are fewer than 3 games, feature all of them.

Tonight shows today's featured games first. Under them, a "More games tonight" section lists today's other games, which can also be picked. If there are no games today, Tonight says "No games tonight — back tomorrow".

All game prices stay at 50% / 50% for now; real prices come in the next step.

Done when:
- Tonight shows today's real NBA games with tip-off times in local time
- The games table holds today's and yesterday's games, yesterday's with final scores
- The 3 featured games stay the same after reopening the app
```

## Step 5 — Prices from the market

Before sending: sign up at the-odds-api.com, copy your API key, and add it in Bilt as a server secret named `ODDS_API_KEY`. Never paste the key into the chat.

```
Add real prices to the games.

Add a server automation called sync-prices, run from sync-games at most once every 6 hours. It calls:
https://api.the-odds-api.com/v4/sports/basketball_nba/odds?regions=us&markets=h2h&oddsFormat=decimal&apiKey=ODDS_API_KEY
using the server secret ODDS_API_KEY.

Match each game in the response to our games table by team names, and by a tip-off time within 12 hours of ours. Team names can differ slightly between the two sources (for example "LA Clippers" and "Los Angeles Clippers"), so compare names after removing "Los Angeles"/"LA" and city words, or keep a small alias list. In the first bookmaker's outcomes, find each team by its name (not by its position in the list), take its decimal price, then compute:
raw_home = 1 / home_decimal, raw_away = 1 / away_decimal
home_price = raw_home / (raw_home + raw_away), away_price = raw_away / (raw_home + raw_away)
Store home_price and away_price on the game. Only update prices of games that haven't started.

If a game has no price, keep 50% / 50% and show a small "price pending" label on its card.

Change how the Daily 3 is chosen, still once per day and then frozen: the first time today's prices are loaded, pick the 3 games whose home price is closest to 50%, among games that haven't started. If prices aren't available, keep the first 3 by tip-off time.

Existing picks keep the price they were made at.

These names are internal only. In the app, always say "price"; never show the words odds, bet or bookmaker names.

Done when:
- Cards show prices that differ from 50% / 50% and add up to 100%
- The payout under each team matches 1 / price − 1
- Existing picks kept their original price
```

## Step 6 — Results and P&L

```
Settle picks automatically.

In sync-games, for every game that is final: set its winner (the team with the higher score). Then settle every pending pick on a final game: if the pick's side is the winner, result = won and P&L = 1 / price − 1, rounded to 2 decimals; otherwise result = lost and P&L = −1.

In My picks, show for each pick: the teams, the live or final score, the price at pick time, and the result (Pending, Won +1.63, Lost −1.00). The total P&L at the top sums all settled picks. Under it add a "Today" line: the sum of the P&L of picks settled today, e.g. "Today: +0.85".

Done when:
- After setting a game with a pick to final with scores in the database and reopening the app, the pick shows Won or Lost
- For any won pick, P&L = 1 / price − 1 (a pick at 38% shows +1.63)
- The total P&L adds up
```

## Step 7 — Leagues

```
Add private leagues on the Leagues tab.

Create a leagues table (name, 6-character unique invite code, creator, created date) and a league members table (league, user, joined date).

The Leagues tab lists the leagues the user belongs to, with two buttons at the top: "Create a league" and "Join with a code".
- Create: enter a name; the league is created with a random invite code and the creator is its first member. Then show the code with an "Invite friends" button that opens the phone's share sheet with this message: "Join my league [league name] on ScoreVault — code: [code]".
- Join: enter a code; if it exists, the user joins and lands on the league; if not, show "No league with this code".

Tapping a league opens its screen: name, invite code with the share button, and the list of members with avatar and name.

Visibility rules from now on:
- Any signed-in user can find a league by its exact invite code, in order to join it.
- A user can see the profile (name, avatar) and the picks of anyone who shares at least one league with them.
- Users can still only change their own picks and profile.

Done when:
- Two test accounts end up in the same league using the code, and each sees the other's name and avatar
- The share button opens the share sheet with the message and the code
- A wrong code shows "No league with this code"
```

## Step 8 — League leaderboard

```
On the league screen, replace the plain member list with a leaderboard.

Rank members by total P&L in units over all their settled picks, highest first. Each row: rank, avatar, name, total P&L (green or red), and today's P&L in smaller text. Highlight the current user's row.

Add a toggle at the top: "All time | This week" (this week = picks on games since Monday).

Tapping a member opens a simple list of their picks, newest first, with the same layout as My picks.

Done when:
- The ranking matches each member's P&L
- The toggle changes the ranking
- Tapping a member shows their picks
```

## Step 9 — Feed

```
Build the Feed tab: picks from people who share a league with the user, newest first.

Each item: avatar, name, the pick ("picked Knicks"), the game, the price at pick time as a percentage, and what it pays ("at 38% · pays +1.63"). When the game is final, the item shows the result: "Won +1.63" in green or "Lost −1.00" in red.

When the price was under 35%, add a small "Against the crowd" tag.

Pull to refresh. If the user has no leagues, show "Join or create a league to see your friends' picks" with a button to the Leagues tab.

Done when:
- A pick made by a league-mate appears at the top of the feed after refresh
- Finished games show the result on their items
- Picks from people outside the user's leagues never appear
```

## Step 10 — Profile with P&L

```
Build the Profile tab like a trader's profile.

At the top: avatar, name, and the total P&L in large type, green or red.
Under it, a line chart of the cumulative P&L over time, one point per day with settled picks.
Then four stats in a row: Picks (number of settled picks), Hit rate (% won), ROI (total P&L ÷ settled picks, as a %), Streak (consecutive days, up to today, on which the user picked all of that day's featured games).
Then "Best calls": the user's 3 won picks with the highest P&L.

When tapping a member in a league leaderboard, open this same profile view for them instead of the plain list of picks from step 8.

Done when:
- The chart and the four stats match the user's picks
- A league-mate's profile opens from the leaderboard
```

## Step 11 (optional) — Share card

```
On the user's own won picks, in My picks and in Best calls on their own profile, add a "Share" button. It creates a square image card in the app's design: the user's avatar and name, the game, the pick, the price ("Only 38% saw it coming"), the result in large green type ("+1.63"), and the app name at the bottom. Then open the share sheet with the image.

Done when:
- Sharing a won pick produces the image and opens the share sheet
- The button doesn't appear on other people's picks
```

## Step 12 (optional) — Demo league

Only if the league is still empty when you present. Better: on the first evening, get your teammates and other attendees to join your league and pick that night's games, so the demo runs on real picks.

```
Add a "Load demo league" button at the bottom of the Leagues tab, visible only to my account. It creates a league called "Demo league" with 5 sample members, each with a name ending in "(sample)", an emoji avatar, and picks on games from the last 3 days in the games table, with a mix of wins and losses including at least one won pick under 35%. Settle them like real picks. Add a "Remove demo league" button that deletes it all.

Done when:
- The demo league shows a leaderboard with P&L, feed items and profiles
- "Remove demo league" deletes the league, its sample members and their picks
```

---

## Templates for fixes and tweaks

**Something's broken**
```
On the [screen name], [what's wrong].
Steps: 1. [step] 2. [step] 3. [step]
Expected: [what should happen]. Actual: [what happens, with the exact error message].
It worked before [last change].
Fix only this; keep everything else unchanged.
```

**Changing the design**
```
On the [screen name], change [element] to [new style]. Keep everything else unchanged.
```
Attach a screenshot whenever the change is visual.

**Adding something**
```
On the [screen name], add [feature]. When the user [action], it should [behavior].
Keep unchanged: [what must not move].
Done when: [what you'll check in the preview].
```
