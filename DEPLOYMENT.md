# AutoEarn AI — Full Deployment Guide

## Architecture Overview

```
GitHub Pages (Frontend)          Render.com (Backend)
autoearn-ai-upgraded.html  ───►  /api/offers
                                  │
                                  ├─ CJ Affiliate API      (real, needs key)
                                  ├─ Admitad API           (real, needs key)
                                  ├─ Impact.com API        (real, needs key)
                                  ├─ Coinbase Public API   (real, no key)
                                  ├─ Freelancer RSS        (real, no key)
                                  ├─ Upwork RSS            (real, no key)
                                  ├─ DesiDime Scraper      (real, no key)
                                  ├─ GrabOn Scraper        (real, no key)
                                  ├─ CouponDunia Scraper   (real, no key)
                                  ├─ CashKaro Scraper      (real, no key)
                                  └─ Fallback Baseline     (curated, real links)
```

---

## Step 1 — Deploy Backend to Render.com (FREE)

### 1.1 Create GitHub repo for backend
```bash
cd autoearn-backend
git init
git add .
git commit -m "AutoEarn AI backend v1"
git remote add origin https://github.com/YOUR_USERNAME/autoearn-backend.git
git push -u origin main
```

### 1.2 Deploy on Render
1. Go to https://dashboard.render.com
2. Click **"New +"** → **"Web Service"**
3. Connect your GitHub repo (`autoearn-backend`)
4. Settings:
   - **Name:** `autoearn-ai-backend`
   - **Region:** Singapore (closest to India)
   - **Runtime:** Node
   - **Build Command:** `npm install`
   - **Start Command:** `node server.js`
5. Click **"Create Web Service"**

Your backend URL will be: `https://autoearn-ai-backend.onrender.com`

### 1.3 Add Environment Variables (Render Dashboard → Environment tab)
```
NODE_ENV=production
PORT=3001
ALLOWED_ORIGIN=https://YOUR_USERNAME.github.io
```

---

## Step 2 — Add API Keys (One by One)

### CJ Affiliate (Commission Junction)
- Sign up: https://signup.cj.com/member/signup/publisher/
- Free to join. Approval usually instant.
- Get API Key: Dashboard → Account → API Keys
- Revenue: $5–$50 per lead, 5–20% commission
```
CJ_API_KEY=your_key_here
CJ_WEBSITE_ID=your_publisher_id
```

### Admitad (India-focused affiliate network)
- Sign up: https://www.admitad.com/en/affiliate/
- Strong India presence: Flipkart, Myntra, Nykaa, etc.
- Get OAuth token: Tools → API
```
ADMITAD_TOKEN=your_oauth_token
```

### Impact.com
- Sign up: https://app.impact.com/secure/mediapartner/register
- Premium brands: Airbnb, Canva, Semrush, Coursera
- Get SID + Token: Settings → API
```
IMPACT_ACCOUNT_SID=your_account_sid
IMPACT_AUTH_TOKEN=your_auth_token
```

### Amazon PA-API (Associates)
- Sign up: https://affiliate-program.amazon.in/
- Requires 3 qualifying sales in 90 days first
- After approval: Tools → Product Advertising API
```
AMAZON_ACCESS_KEY=your_access_key
AMAZON_SECRET_KEY=your_secret_key
AMAZON_PARTNER_TAG=yourstore-21
```

---

## Step 3 — Deploy Frontend to GitHub Pages

### 3.1 Update API URL in HTML
Open `autoearn-ai-upgraded.html`, find:
```javascript
const API_BASE = window.location.hostname === 'localhost' || window.location.hostname === '127.0.0.1'
  ? 'http://localhost:3001'
  : 'https://autoearn-ai-backend.onrender.com';
```
Replace `autoearn-ai-backend.onrender.com` with your actual Render URL.

### 3.2 Push to GitHub
```bash
# In your existing repo (sreenathsjk.github.io or E-Money)
cp autoearn-ai-upgraded.html index.html
git add index.html
git commit -m "Upgrade to real-time API system"
git push origin main
```

### 3.3 Enable GitHub Pages
- Settings → Pages → Source: main branch, / (root)
- Site live at: `https://sreenathsjk.github.io`

---

## Step 4 — Test Everything

```bash
# Test backend health
curl https://autoearn-ai-backend.onrender.com/api/health

# Test offers endpoint
curl https://autoearn-ai-backend.onrender.com/api/offers | python3 -m json.tool | head -100

# Test force refresh
curl https://autoearn-ai-backend.onrender.com/api/offers?force=true

# Check which sources are active
curl https://autoearn-ai-backend.onrender.com/api/sources
```

---

## Data Sources: What's Real vs What's Fallback

| Source | Type | Auth Required | Data Type | Revenue |
|--------|------|--------------|-----------|---------|
| CJ Affiliate | REST API | ✅ API Key | Real affiliate programs | 5–20% commission |
| Admitad | REST API | ✅ OAuth Token | India/global programs | 3–15% commission |
| Impact.com | REST API | ✅ SID + Token | Premium brands | 5–25% commission |
| Amazon PA-API | REST API | ✅ Access Keys | Products + affiliate | 1–10% commission |
| Coinbase | Public API | ❌ None | Crypto earn programs | Direct referral |
| Freelancer RSS | RSS Feed | ❌ None | Live freelance jobs | Traffic value |
| Upwork RSS | RSS Feed | ❌ None | Live freelance jobs | Traffic value |
| DesiDime | Web Scraper | ❌ None | India cashback deals | Affiliate clicks |
| GrabOn | Web Scraper | ❌ None | India cashback offers | Affiliate clicks |
| CouponDunia | Web Scraper | ❌ None | India coupons/offers | Affiliate clicks |
| CashKaro | Web Scraper | ❌ None | India store cashback | Affiliate clicks |
| **Fallback Baseline** | **Curated** | **❌ None** | **Real verified links** | **All channels** |

**The fallback baseline activates ONLY when live sources return < 10 results.**
It contains 12 real, verified, high-ROI programs with correct links.

---

## AI Scoring Formula

```
aiScore = (reward/maxReward × trustScore × freshness / effort) × 100

Where:
  reward     = cashback/earning value (normalised to max in batch)
  trustScore = source reliability (0–1), set per source
  freshness  = how recent (0–1), RSS feeds score 0.96–0.97
  effort     = 1 (low) | 2 (medium) | 3 (high)

Result: 0–100 score. Sorted DESC.
```

---

## Caching System

```
Request → Check in-memory cache (6 min TTL)
       → HIT:  return cached data immediately
       → MISS: run full pipeline (5–15 sec), cache result, return

force=true → bypass cache, always run fresh pipeline
```

---

## Revenue Setup (Monetisation)

Once backend is live, replace hardcoded `url` fields with your affiliate links:

```javascript
// In server.js AFF_LINKS object or per-source adapters:
// Zerodha: https://zerodha.com/open-account?c=YOUR_CODE  → ₹300/account
// Groww:   https://groww.in/refer?ref=YOUR_CODE          → ₹100–₹250/signup
// Upstox:  https://upstox.com/open-account/?f=YOUR_CODE  → ₹400/account
// Coinbase: https://coinbase.com/join/YOUR_CODE          → $10/signup
// Binance:  https://accounts.binance.com/register?ref=X  → 20% fee share
```

---

## Monthly Revenue Projection

| Month | Users | Affiliate Clicks | Conversion | Revenue |
|-------|-------|-----------------|------------|---------|
| M1 | 1,000 | 300 | 5% (15 signups) | ₹45,000 |
| M3 | 10,000 | 3,000 | 5% (150 signups) | ₹4,50,000 |
| M6 | 50,000 | 15,000 | 5% (750 signups) | ₹22,50,000 |
| M12 | 1,00,000 | 30,000 | 5% (1500 signups) | ₹45,00,000 |

*Based on ₹300 avg commission per signup across platforms*

---

## Local Development

```bash
cd autoearn-backend
npm install
cp .env.example .env
# Edit .env with your keys
npm run dev   # starts on port 3001 with nodemon

# In browser, open autoearn-ai-upgraded.html
# It auto-detects localhost and calls http://localhost:3001
```
