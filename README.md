# TempTrader v3.1

Private Kalshi daily temperature bracket trading tool.

## Setup

### GitHub Pages (Recommended — works on any device)
1. Push this repo to GitHub (private repository)
2. Go to Settings → Pages → Source: Deploy from branch → main → / (root)
3. Your tool will be live at `https://yourusername.github.io/repo-name/`
4. Bookmark that URL on your phone — works like an app

### Local (Won't work — CORS blocks all API calls)
Opening `index.html` directly from your filesystem will fail.
You must use a hosted URL (GitHub Pages, or a local web server).

### Local web server alternative
```bash
cd /path/to/repo
python3 -m http.server 8080
# Then open: http://localhost:8080
```

## Features

- **Markets** — Live full bracket ladder from Kalshi for all temperature cities. Visual probability distribution, buy zone detection (⭐ 15-35¢), place limit orders directly.
- **Positions** — Pull your live positions from Kalshi, P&L tracking, AI synopsis (🟢/🟡/🔴 per position), place GTC exit orders.
- **Orders** — View and cancel all resting orders, recent fills.
- **Scan** — All cities simultaneously: IEM live temp + Kalshi favorite bracket price, E-wind flags, marine cap flags, buy zone count.
- **Analyze** — Claude AI AFD analysis + local quick mode + T-field boundary parser.
- **Monitor** — Live IEM ASOS data feed (5-min updates), signal engine (IN BRACKET/REVERSAL/BUSTED), auto-refresh.

## Cities Supported

**HIGH Markets:** PHX, ATL, DFW, AUS, SAT, OKC, DEN, SEA, LAX, SFO, NYC, BOS, DC, PHL, CHI, MIA  
**LOW Markets:** PHX, DFW, AUS, OKC, DEN, LAX, SFO, SEA, CHI, BOS, MIA, HOU

## Key Trading Rules Encoded

- Kalshi % = last traded price (¢), NOT consensus probability. Driven by Kalshi model.
- 5-min ASOS = coin flip ambiguity (skier219 rule). Trust hourly readings (:51 mark) only.
- Preliminary CLI can be incorrect. 6hr NWS max overrides it (Trackhead rule).
- PHX/LAS spring: AFD is a floor not ceiling. CLR + SSW + top of range before 3pm = buy bracket above.
- E-wind East Coast: -5 to -8°F discount on AFD. Gulf: -2 to -4°F.
- BOS SW warm advection: high can hit 5-6pm EDT. Never exit before 6pm EDT.
- LOW markets: sell before 9pm PST. Never hold to resolution.
- T-field boundary within 0.1°F = genuine coin flip. Max 25 contracts.
- If contract is at a loss: Kalshi model often reprices upward when lower bracket becomes likely → better exit. Wait for slight recovery.

## Tech Notes

- Single HTML file, no dependencies, no build step
- RSA-PSS signing via Web Crypto API (PKCS8 format required)
- IEM ASOS data: mesonet.agron.iastate.edu (free, no auth, CORS-friendly)
- Kalshi public market data: no auth required
- Kalshi portfolio/orders: RSA-signed requests
- Claude API calls: go through claude.ai artifact bridge when opened in Claude, or direct when hosted
