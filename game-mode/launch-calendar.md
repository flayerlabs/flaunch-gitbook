---
icon: calendar-clock
---

# Launch calendar

Game Mode launches on Flaunch share one calendar, so two rounds of the same game do not fight for the same players.

When a creator schedules a Game Mode launch, the picker shows start times in five-minute steps for the next 24 hours. Some are greyed out as unavailable. Whether a time is open depends on the game they picked, not on a fixed grid: each game decides how long a launch occupies the calendar and how many of its rounds may overlap.

### How a launch occupies the calendar

A launch takes up the calendar from its start until its round ends, plus a short grace period:

```
occupied = [ start, start + round length + grace )
```

* **Round length** comes from your gate's `/config` (`roundDurationMs`), frozen when your game was approved on that network. A 90-second arcade round occupies a minute and a half; a week-long strategy game occupies a week.
* **Grace** is set by Flaunch per game (60 seconds by default). It keeps the next launch from starting the instant your round settles.
* **Rounds in parallel** is also set per game (1 by default). A three-minute game usually allows one round at a time, so the next start is at least three minutes plus grace later. A seven-day game might allow twenty overlapping rounds, because nobody expects a week-long round to have the field to itself.

A start time is available while, at every moment of its window, fewer than the allowed number of rounds of that game are already open. A game's calendar spans networks: a round on Base and a round on Robinhood Chain count together.

Launches that do not pick a game share a per-network "default gate" calendar with its own limits.

### What creators see

* Picking a time **holds** it for ten minutes while the form is filled in. Another creator sees it as unavailable. Re-picking moves the hold; closing the form releases it.
* Launching **confirms** the hold with the coin's address, and the time stays occupied until the round and its grace end.
* If the calendar cannot be reached, the picker still works. Every time shows as available with a warning that the launch may overlap another round. A paid launch is never blocked by a scheduling outage.
* Whether testnet launches are governed depends on the enforcement scope below.

### Launches made elsewhere

The calendar governs launches made through flaunch.gg. Other launchers are free to run their own queues; a round they start does not block a time here, and a Flaunch reservation does not block them. If a Flaunch launch's confirmation is lost in transit, the backend still recognises the coin when the game's gate reports the round and keeps the time occupied.

### Scope and the off switch

Flaunch can limit enforcement to mainnet chains, to testnets, or run it everywhere, and can switch it off entirely. When it is off, or a chain is out of scope, every time is available and nothing is held. Base Sepolia rehearsals run with enforcement on testnets only, so mainnet launches are untouched.

### For game developers

You do not configure any of this in your game or gate. Your round length is read from `/config`, and Flaunch sets grace and parallel rounds when your game is reviewed. If your game is designed for long, overlapping rounds, say so in your submission so the limit is set to match. If you change `roundDurationMs` in a new build, the calendar picks up the new length once that revision is approved; launches already on the calendar keep the window they were given.
