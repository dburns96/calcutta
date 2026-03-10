# 🏀 Calcutta Draft 2026

A web app for running a March Madness Calcutta auction draft — built for a private league of 8 pairs bidding on all 56 tournament items in real time.

## What is a Calcutta?

A Calcutta is an auction-style betting pool where every team in the tournament is sold to the highest bidder. Payouts are based on how far your teams advance, calculated as a percentage of the total pot. Because the pot size isn't known until all bidding is complete, a key challenge is estimating fair bid prices in real time.

## How Our League Works

- **Format:** 8 pairs bidding; teams auctioned one at a time in random order
- **Auction items (56 total):**
  - Seeds 1–10 and 12–13: individual teams (48 items)
  - Seed 11: 2 individual + 2 play-in bundles (guaranteed live team) (4 items)
  - Seeds 14/15/16: one regional bundle per region (4 items)
- **Payout structure (stacking percentages of total pot):**
  | Round | Payout |
  |-------|--------|
  | R64 Win | 1.0% |
  | R32 Win | 1.5% |
  | Sweet 16 Win | 2.5% |
  | Elite 8 Win | 3.0% |
  | Final Four Win | 4.0% |
  | Champion | 4.0% |
  - A national champion earns its owner **16% of the total pot**
- **Historical pot size:** ~$9,000–$10,000

## App Features

### Setup Tab
- Download a CSV template pre-populated with all 56 auction items
- Fill in real team names and round-by-round probabilities from three sources after Selection Sunday
- Upload the filled CSV to load all data at once
- Adjust source weights with sliders
- Manual per-team probability editing also available

### Draft Night Tab (three-column layout)
**Left — Pot Estimator**
- Manual pot estimate (adjustable)
- Market estimate: back-calculated from observed bids using IQR outlier filtering
- Toggle between manual and market estimate
- Live bid log with implied pot per bid

**Center — Team Evaluator**
- Search for any team and click to load analysis
- Fair bid shown prominently at top (primary number)
- Bid entry: enter final price, then "Someone Else Won" or "I Won It!"
- Bid input auto-focuses on team selection for speed
- Below bid entry: EV% by source, round-by-round breakdown, portfolio fit analysis
- Teams disappear from the list once a bid is logged

**Right — Portfolio**
- Teams owned, total spent, total EV, expected P&L
- Coverage profile: probability that at least one owned team reaches Sweet 16, Elite 8, Final Four, Championship

## Data Sources

Three probability sources are averaged with configurable weights:

| Source | Type | Cost |
|--------|------|------|
| [KenPom](https://kenpom.substack.com) | Efficiency model | Free (Substack) |
| [EvanMiya](https://evanmiya.com) | Efficiency model | Free |
| [TeamRankings](https://teamrankings.com) | Market/betting-derived | Free |

Probabilities are entered as round-by-round win percentages: R64win, R32win, S16win, E8win, F4win, NCGwin.

## Tech Stack

- **Frontend:** Single self-contained HTML file (React via CDN, no build step)
- **Backend/Database:** Supabase (planned)
- **Hosting:** GitHub Pages

## Repo Structure

```
/
├── index.html          # The full app — everything lives here
├── README.md           # This file
├── CLAUDE.md           # AI assistant context and guidelines
```

## Local Development

No build tools needed. Just open `index.html` in any browser.

## Deployment

Hosted via GitHub Pages at: `https://dburns96.github.io/calcutta`

## Season History

| Year | Pot | Result |
|------|-----|--------|
| 2024 | ~$9,000 | +$500–600 profit (Clemson Elite 8, Illinois) |
| 2025 | ~$10,000 | -$319 total (-$159.55 each) |
