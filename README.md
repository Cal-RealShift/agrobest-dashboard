# AgroBest Finance Dashboard Demo

Demo dashboards for AgroBest proposal. Ready to deploy to GitHub Pages.

---

## Quick Deploy (5 mins)

### Option A: GitHub (Free, Permanent)

1. **Create GitHub account** (if needed): https://github.com/signup

2. **Create new repository**:
   - Go to https://github.com/new
   - Name: `agrobest-dashboard`
   - Set to **Public**
   - Click "Create repository"

3. **Upload files**:
   - Click "uploading an existing file"
   - Drag all `.html` files from this folder
   - Commit: "Dashboard demo"

4. **Enable GitHub Pages**:
   - Go to repo Settings > Pages
   - Source: "Deploy from a branch"
   - Branch: `main` (or `master`), folder: `/ (root)`
   - Save

5. **Your URL** (live in 1-2 mins):
   ```
   https://YOUR-USERNAME.github.io/agrobest-dashboard/
   ```

---

### Option B: Netlify Drop (30 seconds)

1. Go to https://app.netlify.com/drop
2. Drag this entire folder onto the page
3. Instant URL generated

---

## Files Included

| File | Description |
|------|-------------|
| `index.html` | Landing page with links to both dashboards |
| `Dashboard-1-Executive-Finance.html` | P&L, budget vs actual, cash position |
| `Dashboard-2-Customer-Insights.html` | Top customers, concentration risk, seasonality |
| `data/` | Source CSV files (for reference, not needed for demo) |

---

## Data Sources (in /data folder)

| File | Content |
|------|---------|
| `Sales Actual Vs Budget.csv` | FY22/23 customer sales by month |
| `2025 Final Budget.csv` | FY24/25 full P&L budget |
| `24-25 Cash Projection Oct2024.csv` | Cash flow projection from Oct 2024 |

---

## Customization

### Change branding
Edit `index.html` lines 70-72:
```html
<div class="logo">REALSHIFT</div>  <!-- Your logo text -->
<div class="badge">DEMO PREVIEW</div>
<h1>Finance Dashboard Suite</h1>
```

### Change contact email
Edit `index.html` line 120:
```html
<a href="mailto:your@email.com">your@email.com</a>
```

---

## Pricing Context

This demo supports the following proposal:

| Package | Upfront | Monthly |
|---------|---------|---------|
| Finance Dashboard (basic) | $2,500 | $50 |
| + AI Insights tier | +$1,000 | +$149 |
| HubSpot Integration | $2,000 | $150 |

The **AI Insights panels** (blue boxes) represent the +$149/mo tier.

---

## Support

RealShift
barcley@realshift.com.au
