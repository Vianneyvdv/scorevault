# Step 1 — First build

Paste the block below into Bilt as the very first message of a new project. It builds the app's shell and one complete journey (open the app → see tonight's games with prices → make 3 picks → see them with their potential P&L) on sample data. The backend and the real data come in the next steps (`build-steps.md`).

Replace `ScoreVault` with the final name if it's chosen by then.

---

```
Build a social sports picks app for groups of friends in the US, called ScoreVault. Think of it as a trading app where every sports pick is a position: each side of a game has a price (the probability it wins), and every player has a P&L. For this first version, only NBA basketball, free to play, no money.

Primary user: a 22–35 year-old sports fan who already argues about games in a group chat with friends.

Core outcome of this first build: the user opens the app, sees tonight's 3 featured games with a price on each side, makes 3 picks in under 30 seconds, and sees what each pick would pay.

The main features are:
1. Tonight screen (home): the "Daily 3", three game cards. Each card shows the two teams, tip-off time, and two buttons, one per team, each showing its price as a percentage (e.g. "Celtics 62%" / "Knicks 38%"). Tapping a team selects it; tapping the other team switches the pick. Under each team button, show what that pick pays if right: payout = 1 / price − 1 units, shown like "+0.61 if right" under Celtics 62%. A progress line at the top shows "2 of 3 picked".
2. Picks lock at tip-off: after the start time, the card shows "Locked" and the buttons are disabled.
3. My picks: a second view on the Tonight tab, reached with a "Tonight | My picks" toggle at the top. It lists the user's picks with team, price at the time of the pick, and status (Pending, Won +0.61, Lost −1.00). A total P&L at the top, in units, green if positive, red if negative.
4. Bottom navigation with 4 tabs: Tonight, Feed, Leagues, Profile. For now, Feed, Leagues and Profile are simple placeholder screens with a title and one line saying what will come ("Your friends' picks will appear here", "Create or join a league", "Your stats and P&L").

User experience: the app opens directly on Tonight. No sign-in yet. Picks are kept on the device for now.

Use this SAMPLE DATA, clearly treated as sample data, for tonight's games (tip-off times 7:00 PM, 7:30 PM and 10:00 PM Eastern, today):
- Boston Celtics (home, 62%) vs New York Knicks (away, 38%)
- Denver Nuggets (home, 55%) vs Los Angeles Lakers (away, 45%)
- Golden State Warriors (home, 41%) vs Oklahoma City Thunder (away, 59%)
Add two past sample picks in My picks so the P&L shows: one won (Thunder at 59%, +0.69) and one lost (Lakers at 45%, −1.00).

Design: clean, light and minimal, in the spirit of Polymarket and Revolut. White background, black text, one accent color for selected states, green for gains and red for losses, nothing else colorful. Large readable numbers for prices and P&L. Rounded cards with subtle borders, no gradients, no shadows. Team abbreviations in bold (BOS, NYK). It should feel like a finance app, not a casino: never use the words bet, betting, odds, wager or gamble anywhere in the app.

Done when:
- Tonight shows the 3 sample games with both prices and the payout under each team
- Selecting a team highlights it and updates "x of 3 picked"
- My picks shows the 2 past picks and the total P&L (−0.31 units, in red)
- All 4 tabs open from the bottom navigation
```

---

**Check before moving on:** go through the 4 "Done when" points in the live preview. If one fails, fix it with the bug template in `build-steps.md` before step 2.
