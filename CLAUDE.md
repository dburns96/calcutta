# CLAUDE.md — AI Assistant Guidelines

This file provides context for AI assistants (Claude, Copilot, etc.) working on this repository. Read this before making any changes.

---

## Project Overview

This is a single-page web app for running a March Madness Calcutta auction draft. It is built and maintained by a non-engineer owner (dburns96) with AI assistance. Code changes should be explained clearly and avoid unnecessary complexity.

## Architecture Decisions — Do Not Change Without Discussion

### Single HTML File
The entire app lives in `index.html`. This is intentional. The owner is not a software engineer and needs to be able to understand, share, and deploy the app without a build system. Do not refactor into multiple files, introduce a bundler (Webpack, Vite, etc.), or add a `package.json` unless explicitly asked.

### React via CDN
React is loaded from a CDN script tag, not npm. Babel standalone handles JSX transpilation in the browser. This is not a performance-optimized production setup — it is the correct tradeoff for this project's needs.

### No TypeScript
Keep all code in plain JavaScript. Type safety is not a priority here; clarity and simplicity are.

---

## Domain Knowledge

### League Format
- 8 pairs of bidders (16 people total)
- 56 auction items total, sold one at a time in random order
- Seeds 1–10 and 12–13: individual teams (48 items)
- Seed 11: 2 individual + 2 play-in bundles (4 items)
- Seeds 14/15/16: one regional bundle per region (4 items)
- Regions: East, West, South, Midwest
- Play-in regions (with bundled 11-seeds): South and Midwest

### Payout Structure
Payouts are stacking percentages of the total pot:
- R64 Win: 1.0%
- R32 Win: 1.5%
- Sweet 16 Win: 2.5%
- Elite 8 Win: 3.0%
- Final Four Win: 4.0%
- Champion: 4.0%
- **National champion total: 16% of pot**
- No payout for play-in game wins

### Key Calculations

**Expected Value (EV):**
`EV% = Σ (prob_of_winning_round_i × payout_for_round_i)` for i in [R64, R32, S16, E8, F4, NCG]

**Weighted EV:**
Average of EV% across sources, weighted by user-configured source weights.

**Fair Bid:**
`Fair Bid = Weighted EV% × Estimated Pot`

**Market Pot Estimation:**
Each observed bid implies a pot: `Implied Pot = Bid Price / Team EV%`
Average of all implied pots, with IQR outlier filtering (removes values outside Q1 - 1.5×IQR and Q3 + 1.5×IQR).

**Portfolio Coverage:**
Probability that at least one owned team reaches a given round:
`P(at least one) = 1 - Π(1 - P(team_i reaches round))`
where the product is over all owned teams.

### Data Sources
Three probability sources, each providing round-by-round win probabilities:
- **KenPom** (kenpom.substack.com) — efficiency-based model
- **EvanMiya** (evanmiya.com) — efficiency-based model  
- **TeamRankings** (teamrankings.com) — market/betting-derived

Probabilities are entered as percentages (e.g. `99.3` not `0.993`) and stored internally as decimals.

---

## UI/UX Priorities

The app is used in a fast-moving live auction environment. Speed and clarity of the core workflow matter more than feature completeness.

**Priority order for the Draft Night center panel:**
1. Fair bid (the number the owner needs right now)
2. Bid entry inputs and buttons
3. EV breakdown by source
4. Round-by-round probabilities
5. Portfolio fit analysis (informational, not primary)

The portfolio fit section shows Sweet 16 and Elite 8 coverage prominently (actionable for mid-seeds), and Final Four / Champion coverage as secondary context only. The portfolio note shows a small bid modifier (capped at ±10%) as a footnote, not a headline.

---

## Style Guidelines

- **Colors:** Defined in the `C` object at the top of the file. Always use these variables, never hardcode hex values elsewhere.
- **Inline styles:** The app uses inline React styles throughout. Keep this consistent — do not introduce CSS classes or a separate stylesheet.
- **Font:** Segoe UI / system-ui. Monospace for all numbers and financial figures.
- **Tone:** Dark theme, data-dense, functional. Not decorative.

---

## Current State & Roadmap

### Working
- Full 56-team auction structure with correct bundle logic
- CSV template download and upload for probability data
- Three-source weighted EV calculation
- IQR-filtered market pot estimation from observed bids
- Portfolio coverage profile (Sweet 16, Elite 8, Final Four, Champion)
- Portfolio fit section with bid modifier (capped ±10%)
- Bid entry flow: search → select → enter price → "I Won It" or "Someone Else Won"
- Bid log with implied pot per bid
- Teams disappear from list once bid is logged

### Planned
- Supabase backend integration for shared real-time data between two users
- Persistent storage of bids and team data across sessions
- Possibly: live sync so both users see the same bid log simultaneously

### Known Tradeoffs
- All state is in-memory; refreshing the page loses all data (to be fixed with Supabase)
- Source weights are not persisted between sessions
- Portfolio coverage calculation assumes team independence (ignores regional bracket overlap — acceptable approximation)

---

## What To Ask The Owner Before Making Changes

- Any change to the payout percentages or auction structure
- Any change to how the market pot is estimated
- Any refactoring that changes the single-file architecture
- Adding any new dependencies beyond what's already loaded via CDN
- Any change to the CSV import/export format (would break existing files)
