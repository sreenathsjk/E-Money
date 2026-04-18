# AutoEarn AI — Premium Money Intelligence Dashboard

A single-file, zero-dependency HTML dashboard that surfaces 100 AI-scored earning opportunities across India and Global markets — with deep direct links and step-by-step claim guides for every offer.

---

## Features

- **100 curated offers** across 8 categories: Cashback, Refer & Earn, Invest, Freelance, Survey, Crypto, AI Tools, Apps
- **AI scoring engine** — every offer is ranked by a formula combining reward size, trust rating, freshness, and effort level
- **Live re-ranking** — scores have small random variance on each render and auto-refresh every 5 minutes to simulate live intelligence
- **How to Claim steps** — each offer modal shows a 5-step numbered guide so users know exactly what to do
- **Direct Link badge** — modals display the exact deep URL (not just the homepage) so users land on the right page
- **Filter & search** — filter by All / India / Global / Instant / High Pay; full-text search across title, description, source and tags
- **Skeleton loading** — shimmer placeholders shown on first load and on refresh for a polished UX
- **Fully responsive** — mobile-first layout using Tailwind CDN; works on all screen sizes
- **Show More pagination** — sections initially show 20 offers with a "Show X More" button to expand
- **Ambient design** — animated gradient orbs, grain texture overlay, gold grid background, scrolling ticker

---

## File Structure

```
autoearn-ai.html   ← entire app (HTML + CSS + JS, single file)
README.md          ← this file
```

No build step. No npm. No server required.

---

## How to Use

1. Open `autoearn-ai.html` in any modern browser
2. Use the filter pills (All / India / Global / Instant / High Pay) to narrow offers
3. Search by keyword to find specific platforms or offer types
4. Click any card or **View Offer →** to open the detail modal
5. In the modal: read the How to Claim steps, then click **→ Go to Offer Page** to open the direct link
6. Hit **Refresh AI** to re-score and re-rank all offers with updated freshness variance

---

## Offer Database Schema

Each offer in the `DB` array has the following fields:

| Field | Type | Description |
|---|---|---|
| `id` | number | Unique offer ID (1–100) |
| `title` | string | Offer name |
| `desc` | string | Short description shown on the card |
| `reward` | number | Numeric reward value |
| `cur` | string | Currency symbol (`₹` or `$`) |
| `ef` | 1 / 2 / 3 | Effort level: 1 = Low, 2 = Medium, 3 = High |
| `tag` | string | Category tag (cashback, refer, invest, freelance, survey, crypto, ai, app) |
| `region` | string | `india` or `global` |
| `type` | string | Display section: `instant`, `highpay`, `trending`, `ai-picks` |
| `src` | string | Source platform name |
| `trust` | 0–1 | Trust score (used in AI ranking) |
| `fresh` | 0–1 | Freshness score (used in AI ranking) |
| `link` | string | Deep direct URL to the offer page |
| `deepLabel` | string | Human-readable label of the direct URL |
| `howTo` | string[] | 5-step array explaining exactly how to claim the offer |

---

## AI Scoring Formula

```
score = (reward / MAX_REWARD) × trust × freshness / effort
```

- Scores are normalized to a 0–100 scale
- A small random variance (±1.5 pts) is added on each render to simulate live re-ranking
- Freshness also drifts slightly (±0.04) on each render
- Full re-score runs every 5 minutes automatically

---

## Customisation

**Add a new offer:** append an object to the `DB` array in the `<script>` block following the schema above.

**Change scoring weights:** edit the `score()` function near the top of the script section.

**Add a new filter:** add a `<button class="fpill" data-f="yourkey">` in both `#fbar` and `#mfbar`, then add a corresponding filter condition in the `filtered()` function.

**Change the refresh interval:** edit the `setInterval` call at the bottom of the script (currently `5 * 60 * 1000` ms).

---

## Dependencies

| Dependency | How loaded | Purpose |
|---|---|---|
| Tailwind CSS | CDN | Utility class layout & spacing |
| Google Fonts (Playfair Display, Outfit) | CDN | Typography |

No JavaScript libraries. All logic is vanilla JS.

---

## Browser Support

Works in all modern browsers (Chrome, Firefox, Safari, Edge). Requires ES6+. Does not support IE.

---

## Disclaimer

This dashboard is for **educational and research purposes only**. Offer availability, reward amounts and terms change frequently — always verify on the official platform before acting. This is not financial advice.

