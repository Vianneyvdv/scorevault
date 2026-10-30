# Step 1 — First build

Paste the block below into Bilt as the very first message of a new project, and **attach your 4 FOMO screenshots** to the same message (leaderboard, friends leaderboard, home, trader profile). Bilt's own guide says screenshots work far better than descriptions. The screenshots stay on your phone; they're not stored in this repo.

It builds the app's shell and one complete journey (open the app → see tonight's games with prices → make 3 picks → see what they pay and your P&L) on sample data. The backend and the real data come in the next steps (`build-steps.md`).

Replace `ScoreVault` with the final name if it's chosen by then.

---

```
Build a social sports picks app for groups of friends in the US, called ScoreVault. It works like a social trading app: every sports pick is a position, each side of a game has a price (the probability it wins), and every player has a P&L. For this first version, only NBA basketball, free to play, no money.

Primary user: a 22–35 year-old sports fan who already argues about games in a group chat with friends.

Core outcome of this first build: the user opens the app, sees their P&L, sees tonight's 3 featured games with a price on each side, makes 3 picks in under 30 seconds, and sees what each pick would pay.

The main features are:
1. P&L header at the top of the Tonight tab, like the balance at the top of a trading app: "Your P&L" with the total in units in large type (green if positive, red if negative), and under it a smaller line with today's change.
2. Tonight (home tab): the "Daily 3", three game cards. Each card shows the two teams with their abbreviation in bold, tip-off time, and two buttons, one per team, each showing its price as a percentage (e.g. "Celtics 62%" / "Knicks 38%"). Tapping a team selects it; tapping the other team switches the pick. Under each team button, show what that pick pays if right: payout = 1 / price − 1 units, shown like "+0.61 if right" under Celtics 62%. A progress line shows "2 of 3 picked".
3. Picks lock at tip-off: after the start time, the card shows "Locked" and the buttons are disabled.
4. My picks: a second view on the Tonight tab, reached with a "Tonight | My picks" toggle under the P&L header. It lists the user's picks like open positions in a trading app: team, game, price at the time of the pick, and status on the right (Pending, Won +0.61 in green, Lost −1.00 in red).
5. Bottom navigation with 4 tabs and icons: Tonight, Feed, Leagues, Profile. For now, Feed, Leagues and Profile are simple placeholder screens with a title and one line saying what will come ("Your friends' picks will appear here", "Create or join a league", "Your stats and P&L").

User experience: the app opens directly on Tonight. No sign-in yet. Picks are kept on the device for now.

Use this SAMPLE DATA, clearly treated as sample data, for tonight's games (tip-off times 7:00 PM, 7:30 PM and 10:00 PM Eastern, today):
- Boston Celtics (home, 62%) vs New York Knicks (away, 38%)
- Denver Nuggets (home, 55%) vs Los Angeles Lakers (away, 45%)
- Golden State Warriors (home, 41%) vs Oklahoma City Thunder (away, 59%)
Add two past sample picks in My picks so the P&L shows: one won (Thunder at 59%, +0.69) and one lost (Lakers at 45%, −1.00).

Design: the attached screenshots are from FOMO, a social trading app. Use them as the reference for layout and patterns only: the big P&L number at the top, rounded cards, pill-shaped filter chips, list rows with an avatar or logo on the left and the number on the right, green for gains and red for losses, a clean bottom navigation with icons. Don't copy their logo, name, icons or exact colors.
The app follows the phone's appearance setting automatically, with a light theme and a dark theme, like Instagram or X.
- Dark theme: near-black background, slightly lighter cards, white text, grey secondary text.
- Light theme: white background, light grey cards, black text, grey secondary text.
- In both themes: one accent color, electric blue, for primary buttons and selected states; green for gains and red for losses, readable on both backgrounds; nothing else colorful.
- Large bold numbers for prices and P&L. Rounded cards with subtle borders, no gradients, no heavy shadows.
It should feel like a finance app, not a casino: never use the words bet, betting, odds, wager or gamble anywhere in the app.

Done when:
- The P&L header shows −0.31 units in red
- Tonight shows the 3 sample games with both prices and the payout under each team
- Selecting a team highlights it in electric blue and updates "x of 3 picked"
- My picks shows the 2 past picks with Won in green and Lost in red
- Switching the phone between light and dark mode switches the app's theme, and both are readable
- All 4 tabs open from the bottom navigation
```

---

**Check before moving on:** go through the "Done when" points in the live preview, in both light and dark mode. If one fails, fix it with the bug template in `build-steps.md` before step 2.
