# AgroBest Pricing Logic & Product Pathing

**Date:** 28 Dec 2024
**Status:** Final structure

---

## Product Separation Rationale

| Dashboard | Primary Buyer | Value Prop |
|-----------|---------------|------------|
| **Finance** | Alistair/Jeff, Callum | Backward-looking: P&L, AR, credit, cash |
| **Foresight** | Saxon, Callum | Forward-looking: opportunities, market intel |

Different stakeholders, different data sources, different automation complexity.

---

## Finance Dashboard Tiers

| Tier | What It Adds | Automation Required |
|------|--------------|---------------------|
| Base | Static KPIs, P&L, AR aging, credit breaches | None (manual refresh) |
| +Finance AI | Live alerts, margin monitoring, CFO matrix | Supabase + n8n jobs |

**Finance AI justifies $149/mo:**
- Exo → Supabase pipeline (ongoing sync)
- n8n workflows for alert generation
- Weekly/daily data refresh maintenance

---

## Foresight Dashboard Tiers

| Tier | What It Adds | Automation Required |
|------|--------------|---------------------|
| Base | Crop calendar, product matrix, static actions | None |
| +Market Intel | Commodity prices, Weather/ENSO | External API scraping |
| +Sales Intel | Live customer actions, pipeline, territory | HubSpot API read |

**Market Intel justifies $99/mo:**
- Commodity scraping (Cotlook, QSL, CBOT)
- BOM weather parsing
- Daily scheduled refreshes

**Sales Intel justifies $99/mo:**
- HubSpot API calls (read-only)
- Customer activity aggregation
- Pipeline visualization refresh

---

## HubSpot Integration Options

| Option | Direction | Use Case |
|--------|-----------|----------|
| HubSpot → Dashboard | Read-only | Powers Foresight Sales Intel |
| HubSpot ↔ Exo | Bidirectional | Full CRM/ERP sync (separate product) |

---

## Buyer Pathways

### Path A: Finance-First
```
Finance Base → +Finance AI → Add Foresight later
```

### Path B: Sales-First
```
Foresight Base → +Market Intel → +Sales Intel → Add Finance later
```

### Path C: Full Suite
```
Both Dashboards (bundle) → All Intel add-ons → HubSpot↔Exo if needed
```

---

## Competitive Positioning

| Competitor | Offering | Price |
|------------|----------|-------|
| Kilimanjaro | HubSpot sync only (36mo lock-in) | $5,500 + $200/mo |
| RealShift | Full stack | $9,000 + $596/mo |

More scope, no lock-in, local support.

---

*v1.0 | 28 Dec 2024*
