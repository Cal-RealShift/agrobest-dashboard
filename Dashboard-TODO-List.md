# Dashboard Implementation To-Do List

**File:** `Dashboard-3-Sales-Actions.html`
**Created:** 26 December 2025
**Supabase Project:** `emgydkspmnaymxpdqpjr`

---

## Supabase Data Audit Summary

### Available Tables (Verified)
| Schema | Table | Records | Status |
|--------|-------|---------|--------|
| `analysis` | `product_performance_summary` | 30+ products | Active |
| `analysis` | `product_profitability` | 30+ products | Active |
| `dim` | `customer_segments` | 30 customers | Active |
| `dim` | `customers` | Full list | Active |
| `dim` | `products` | Full list | Active |
| `external` | `crop_calendar` | 46 entries | Active |
| `insights` | `daily_sales_actions` | Live | Active |
| `weather` | `bom_stations` | 14 stations | Active |
| `weather` | `daily_observations` | Historical | Active |
| `agrobest_exo_dbo` | `stock_groups` | 16 groups | Active |

### Stock Groups (from Supabase)
| ID | Group Name |
|----|------------|
| 1 | CROP PROTECTION |
| 2 | NPK CONCENTRATES |
| 3 | TRACE ELEMENTS |
| 4 | SOIL CONDITIONERS |
| 5 | STARTERS |
| 6 | SPECIALITY PRODUCTS |
| 7 | RAW MATERIALS |
| 8 | FORMULATED |
| 14 | TOLL MANUFACTURE - PESTICIDES |
| 15 | CUSTOM BLENDS |

---

## Phase 1: Product Family Aggregation

### Task 1.1: Create Product Family View in Supabase
**Priority:** CRITICAL
**Effort:** 2 hours

**Problem:** Dashboard shows SKU-level (ENVY 1000L, ENVY 20L) not family-level (ENVY)

**Implementation:**
```sql
-- Create product family aggregation view
CREATE OR REPLACE VIEW analysis.product_families AS
SELECT
    CASE
        WHEN product_description LIKE 'ENVY%' THEN 'ENVY'
        WHEN product_description LIKE 'MP BRILLIANCE%' THEN 'MP BRILLIANCE'
        WHEN product_description LIKE 'MP ENHANCE%' THEN 'MP ENHANCE'
        WHEN product_description LIKE 'PHOZGUARD%' THEN 'PHOZGUARD'
        WHEN product_description LIKE 'POPSTART%' THEN 'POPSTART'
        WHEN product_description LIKE 'PHOZSTART%' THEN 'PHOZSTART'
        WHEN product_description LIKE '%KOMPLETE%' THEN 'KOMPLETE'
        WHEN product_description LIKE 'GREEN BLAST%' THEN 'GREEN BLAST'
        WHEN product_description LIKE 'OT GREEN BLAST%' THEN 'GREEN BLAST'
        WHEN product_description LIKE 'CARBOCAL%' THEN 'CARBOCAL'
        WHEN product_description LIKE 'CHICKPEA%' THEN 'CHICKPEA'
        WHEN product_description LIKE 'SOVEREIGN%' THEN 'SOVEREIGN'
        WHEN product_description LIKE 'OT TURF%' THEN 'OT TURF'
        WHEN product_description LIKE 'OT BIO%' THEN 'OT BIO'
        WHEN product_description LIKE 'AGRODEX%' OR product_description LIKE 'AGRO K%' THEN 'AGRODEX'
        WHEN product_description LIKE 'BROADHECTARE%' THEN 'BROADHECTARE'
        WHEN product_description LIKE 'AN ADVANCED%' THEN 'AN ADVANCED'
        ELSE product_description
    END as product_family,
    stock_group_name,
    SUM(total_revenue) as family_revenue,
    SUM(total_quantity_sold) as family_quantity,
    COUNT(DISTINCT product_code) as sku_count,
    SUM(unique_customers) as total_customers,
    AVG(gross_margin_pct) as avg_margin,
    MAX(last_sale_date) as last_sale,
    SUM(revenue_last_12mo) as revenue_12mo
FROM analysis.product_profitability
GROUP BY 1, 2
ORDER BY family_revenue DESC;
```

**Verification Query:**
```sql
-- VERIFY: Product families aggregated correctly
SELECT product_family, family_revenue, sku_count, avg_margin
FROM analysis.product_families
WHERE family_revenue > 100000
ORDER BY family_revenue DESC;

-- Expected: ENVY shows combined revenue (~$722K), KOMPLETE shows combined (~$2.5M)
```

**Done Criteria:**
- [ ] View created in Supabase
- [ ] Query returns 15+ product families
- [ ] ENVY family shows combined 1000L + 20L revenue
- [ ] KOMPLETE family includes EC KOMPLETE + KOMPLETE

---

### Task 1.2: Update Crop Calendar Tab with Product Families
**Priority:** HIGH
**Effort:** 3 hours

**Current State:** Shows 20 SKUs with size suffix
**Target State:** Show product families with combined seasonality

**Verification Query:**
```sql
-- VERIFY: Crop calendar products match family names
SELECT DISTINCT unnest(products_relevant) as product
FROM external.crop_calendar
ORDER BY 1;

-- Check: Products should be family names (Komplete, Phozstart) not SKUs
```

**HTML Changes Required:**
1. Replace SKU table with family-level table
2. Aggregate monthly percentages across SKUs
3. Show SKU count per family
4. Calculate weighted average margin

**Done Criteria:**
- [ ] Table header says "Product Family" not "Product"
- [ ] No size suffixes (1000L, 20L) in product names
- [ ] Each row shows SKU count (e.g., "ENVY (2 SKUs)")
- [ ] Revenue is sum of all SKUs in family

---

## Phase 2: Customer Actions Enhancement

### Task 2.1: Add Real Dormant Customer Data
**Priority:** HIGH
**Effort:** 2 hours

**Source Query:**
```sql
-- Get actual dormant customers from Supabase
SELECT
    c.accno,
    c.name as customer_name,
    SUM(dt.debitamt - dt.creditamt) as outstanding,
    MAX(dt.transdate) as last_transaction,
    COUNT(DISTINCT dt.seqno) as transaction_count
FROM agrobest_exo_dbo.dr_accs c
LEFT JOIN agrobest_exo_dbo.dr_trans dt ON c.accno = dt.accno
WHERE c.accno NOT IN (
    SELECT DISTINCT customer_id FROM dim.customer_segments
)
GROUP BY c.accno, c.name
HAVING MAX(dt.transdate) < NOW() - INTERVAL '365 days'
ORDER BY SUM(dt.debitamt) DESC
LIMIT 20;
```

**Verification Query:**
```sql
-- VERIFY: Dormant list matches dashboard
SELECT customer_name, outstanding, last_transaction
FROM dormant_analysis
WHERE outstanding > 50000;

-- Expected: UPL NEW ZEALAND ($431K), MCGREGOR GOURLAY ($251K), etc.
```

**Done Criteria:**
- [ ] Win-back section shows real customer names from Supabase
- [ ] Revenue figures match `dormant_customers.csv`
- [ ] Last order dates are accurate
- [ ] Total potential matches ($1.9M)

---

### Task 2.2: Integrate Live Credit Alerts
**Priority:** HIGH
**Effort:** 1 hour

**Data Source:** `insights.daily_sales_actions`

**Verification Query:**
```sql
-- VERIFY: Credit alerts are current
SELECT action_type, customer_name, action_reason, action_date
FROM insights.daily_sales_actions
WHERE action_type = 'CREDIT_ALERT'
AND action_date = CURRENT_DATE
ORDER BY priority_score;

-- Expected: 10 credit alerts including OASIS, GREENSHED, FERNLAND
```

**Done Criteria:**
- [ ] Credit Risk section pulls from `insights.daily_sales_actions`
- [ ] Shows real over-limit amounts (e.g., GREENSHED $28,088)
- [ ] Phone numbers are clickable
- [ ] Updates daily

---

### Task 2.3: Add Customer Detail Expansion
**Priority:** MEDIUM
**Effort:** 4 hours

**Data Sources:**
- `dim.customer_segments` - Segment, region, crop
- `analysis.sales_tracking` - Order history
- `facts.receivables_transactions` - Payment history

**Verification Query:**
```sql
-- VERIFY: Customer segment data complete
SELECT
    customer_name,
    primary_crop,
    region,
    segment_value,
    buying_season
FROM dim.customer_segments
WHERE segment_value = 'high';

-- Expected: 6 high-value customers with complete data
```

**Done Criteria:**
- [ ] Each customer card shows segment (turf/cotton/distributor)
- [ ] Shows region from `dim.customer_segments`
- [ ] Shows buying season pattern
- [ ] Click expands to show order history

---

## Phase 3: Market Intel Enhancement

### Task 3.1: Add Weather Station Data
**Priority:** MEDIUM
**Effort:** 3 hours

**Data Source:** `weather.bom_stations`, `weather.daily_observations`

**Verification Query:**
```sql
-- VERIFY: Weather data available for key regions
SELECT
    station_name,
    state,
    status
FROM weather.bom_stations
WHERE status IN ('Active', 'OpenMeteo');

-- Expected: 14 stations covering NSW, QLD, VIC
```

**Done Criteria:**
- [ ] Regional weather cards show actual stations
- [ ] Darling Downs shows Dalby/Toowoomba data
- [ ] Rainfall data from `weather.daily_observations`
- [ ] Updates from BOM/OpenMeteo feeds

---

### Task 3.2: Connect Crop Calendar to Weather
**Priority:** MEDIUM
**Effort:** 2 hours

**Data Source:** `external.crop_calendar`

**Verification Query:**
```sql
-- VERIFY: Crop calendar has weather-relevant entries
SELECT
    region,
    crop,
    month,
    stage,
    products_relevant,
    notes
FROM external.crop_calendar
WHERE month = EXTRACT(MONTH FROM CURRENT_DATE)
ORDER BY priority;

-- Expected: December entries for cotton (squaring), turf (summer_stress)
```

**Done Criteria:**
- [ ] Current month highlighted in crop calendar
- [ ] Products matched to `external.crop_calendar.products_relevant`
- [ ] Weather triggers linked to crop stages

---

## Phase 4: Customer Map Overhaul

### Task 4.1: Replace SVG with Leaflet.js
**Priority:** CRITICAL
**Effort:** 6 hours

**Implementation:**
```html
<!-- Add to <head> -->
<link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>

<!-- Replace SVG map with -->
<div id="customer-map" style="height: 500px;"></div>

<script>
const map = L.map('customer-map').setView([-28.5, 145], 5);
L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png').addTo(map);

// Add customer markers from Supabase
fetch('/api/customers/geo')
  .then(r => r.json())
  .then(customers => {
    customers.forEach(c => {
      L.marker([c.lat, c.lng])
        .bindPopup(`<b>${c.name}</b><br>Revenue: $${c.revenue}`)
        .addTo(map);
    });
  });
</script>
```

**Verification Query:**
```sql
-- VERIFY: Customer locations available
SELECT
    customer_name,
    region,
    state
FROM dim.customer_segments
WHERE state IS NOT NULL;

-- Expected: 28+ customers with state/region data
```

**Done Criteria:**
- [ ] Map is interactive (zoom, pan)
- [ ] Customer pins are clickable
- [ ] Popup shows customer details
- [ ] Different colors for segments
- [ ] Whitespace regions marked

---

### Task 4.2: Add Crop Region Overlays
**Priority:** MEDIUM
**Effort:** 4 hours

**Data Sources:**
- `external.crop_calendar` - Regions list
- DEA Maps API - Crop boundaries

**Verification Query:**
```sql
-- VERIFY: All crop regions in calendar
SELECT DISTINCT region FROM external.crop_calendar ORDER BY 1;

-- Expected: bundaberg, burdekin, darling_downs, eyre_peninsula,
-- mallee, melbourne_metro, namoi_valley, riverland, sydney_metro, western_nsw
```

**Done Criteria:**
- [ ] Toggle button for crop layers
- [ ] Cotton regions highlighted
- [ ] Turf/urban areas marked
- [ ] Horticulture regions shown

---

## Phase 5: Opportunities Expansion

### Task 5.1: Add Market Research Data
**Priority:** HIGH
**Effort:** 3 hours

**Source:** `Market-Research/02-Crop-Region-Matrix-Template.md`

**New Opportunities to Add:**
| Region | Crop | TAM | Priority |
|--------|------|-----|----------|
| Bundaberg | Avocado | $6.4M | 1 |
| Bundaberg | Macadamia | $7.8M | 1 |
| Riverland | Citrus | $9.9M | 2 |
| Riverland | Almonds | $8.5M | 2 |
| Gippsland | Dairy | $5.0M | 3 |
| Atherton | Tropical | $2.5M | 3 |
| Liverpool Plains | Cotton | $8.0M | 2 |
| Burdekin | Sugarcane | $3.0M | 3 |
| Hunter | Viticulture | $2.0M | 4 |
| Sunraysia | Grapes | $4.5M | 3 |
| Perth Metro | Turf | $12.0M | 4 |

**Verification (Manual):**
- Count opportunity cards in HTML
- Compare TAM figures to Market-Research docs

**Done Criteria:**
- [ ] 11+ opportunity cards displayed
- [ ] TAM figures match research docs
- [ ] Entry strategy text included
- [ ] Priority badges correct

---

### Task 5.2: Add Field Day Calendar
**Priority:** LOW
**Effort:** 1 hour

**Source:** `Market-Research/11-Novel-Data-Sources.md`

**Events to Add:**
| Event | Date | Location |
|-------|------|----------|
| Farm World | Mar 27-29, 2025 | Lardner VIC |
| Henty | Sep 23-25, 2025 | Henty NSW |
| Elmore | Oct 7-9, 2025 | Elmore VIC |

**Done Criteria:**
- [ ] Events section added to Opportunities tab
- [ ] Dates and locations accurate
- [ ] Links to event websites

---

### Task 5.3: Add Agronomist Network Section
**Priority:** LOW
**Effort:** 1 hour

**Source:** `Market-Research/10-Enhanced-Framework.md`

**Done Criteria:**
- [ ] Section showing agronomist channels
- [ ] Crop Consultants Australia listed
- [ ] Australian Agronomist Magazine listed
- [ ] Target count (20 agronomists)

---

## Phase 6: Products Tab Enhancement

### Task 6.1: Add Full Product Catalog
**Priority:** HIGH
**Effort:** 3 hours

**Verification Query:**
```sql
-- VERIFY: All active products with revenue
SELECT
    product_family,
    stock_group_name,
    family_revenue,
    avg_margin,
    sku_count
FROM analysis.product_families
WHERE family_revenue > 50000
ORDER BY family_revenue DESC;

-- Expected: 20+ product families
```

**Done Criteria:**
- [ ] Shows 20+ product families (not SKUs)
- [ ] Grouped by stock_group_name
- [ ] Revenue column accurate
- [ ] Margin column with color coding

---

### Task 6.2: Add Margin Analysis Flags
**Priority:** MEDIUM
**Effort:** 1 hour

**Verification Query:**
```sql
-- VERIFY: Low margin products identified
SELECT
    product_description,
    gross_margin_pct,
    total_revenue
FROM analysis.product_profitability
WHERE gross_margin_pct < 50
AND total_revenue > 100000
ORDER BY gross_margin_pct;

-- Expected: POPSTART PZ (44%), POPSTART NP BULK (58%), PHOZSTART (60%)
```

**Done Criteria:**
- [ ] Products with <30% margin flagged red
- [ ] Products with 30-50% margin flagged amber
- [ ] Products with >50% margin flagged green
- [ ] Tooltip explains margin concern

---

### Task 6.3: Add Cross-Sell Matrix
**Priority:** MEDIUM
**Effort:** 2 hours

**Logic:**
| If Customer Buys | Recommend | Reason |
|------------------|-----------|--------|
| PHOZSTART | POPSTART | Same application |
| KOMPLETE | AGRODEX K | Boll fill combo |
| ENVY | SOVEREIGN | Stress stack |
| MP BRILLIANCE | GREEN BLAST | Turf system |
| PHOZGUARD | Adjuvant | Tank mix |

**Verification Query:**
```sql
-- VERIFY: Product combinations exist
SELECT
    a.product_family as product_a,
    b.product_family as product_b,
    COUNT(DISTINCT a.unique_customers) as shared_customers
FROM analysis.product_families a
CROSS JOIN analysis.product_families b
WHERE a.product_family != b.product_family
GROUP BY 1, 2
HAVING COUNT(*) > 0
ORDER BY shared_customers DESC
LIMIT 20;
```

**Done Criteria:**
- [ ] Cross-sell section added to Products tab
- [ ] 5+ product combinations listed
- [ ] Reason column explains logic

---

## Phase 7: Dashboard Main Tab

### Task 7.1: Add Sparkline Charts
**Priority:** MEDIUM
**Effort:** 2 hours

**Implementation:**
```html
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<!-- Add sparklines to KPI cards -->
```

**Verification Query:**
```sql
-- VERIFY: Monthly revenue data available
SELECT
    DATE_TRUNC('month', order_date) as month,
    SUM(revenue) as monthly_revenue
FROM analysis.sales_tracking
WHERE order_date > NOW() - INTERVAL '12 months'
GROUP BY 1
ORDER BY 1;
```

**Done Criteria:**
- [ ] Each KPI card has mini sparkline
- [ ] Shows 12-month trend
- [ ] Trend arrow (up/down)

---

### Task 7.2: Add Live Data Indicators
**Priority:** LOW
**Effort:** 1 hour

**Done Criteria:**
- [ ] "Last updated" timestamp on dashboard
- [ ] Pulse animation on live data
- [ ] Data source badges

---

## Verification Checklist

### Run These Queries After Implementation

```sql
-- 1. Product families exist
SELECT COUNT(*) as family_count FROM analysis.product_families;
-- Expected: 15+

-- 2. Customer segments complete
SELECT COUNT(*) as segment_count FROM dim.customer_segments;
-- Expected: 30

-- 3. Crop calendar entries
SELECT COUNT(*) as calendar_entries FROM external.crop_calendar;
-- Expected: 46

-- 4. Daily actions generating
SELECT COUNT(*) FROM insights.daily_sales_actions
WHERE action_date = CURRENT_DATE;
-- Expected: 10+

-- 5. Weather stations active
SELECT COUNT(*) FROM weather.bom_stations WHERE status != 'Inactive';
-- Expected: 14
```

### Visual Verification Checklist

| Tab | Check | Status |
|-----|-------|--------|
| Dashboard | Sparklines render | [ ] |
| Dashboard | Live timestamp shows | [ ] |
| Customer Actions | Real dormant data | [ ] |
| Customer Actions | Credit alerts current | [ ] |
| Market Intel | Weather by region | [ ] |
| Market Intel | ENSO status correct | [ ] |
| Crop Calendar | Product families (not SKUs) | [ ] |
| Crop Calendar | No size suffixes | [ ] |
| Customer Map | Interactive zoom/pan | [ ] |
| Customer Map | Clickable markers | [ ] |
| Opportunities | 11+ regions listed | [ ] |
| Opportunities | Field days included | [ ] |
| Products | 20+ families shown | [ ] |
| Products | Margin flags visible | [ ] |

---

## Priority Execution Order

### Week 1: Critical Fixes
1. [ ] Task 1.1 - Product Family View (Supabase)
2. [ ] Task 1.2 - Update Crop Calendar Tab
3. [ ] Task 4.1 - Replace Map with Leaflet.js
4. [ ] Task 2.1 - Real Dormant Customer Data

### Week 2: High Priority
5. [ ] Task 2.2 - Live Credit Alerts
6. [ ] Task 5.1 - Market Research Data
7. [ ] Task 6.1 - Full Product Catalog
8. [ ] Task 3.1 - Weather Station Data

### Week 3: Medium Priority
9. [ ] Task 2.3 - Customer Detail Expansion
10. [ ] Task 3.2 - Crop Calendar Weather Link
11. [ ] Task 4.2 - Crop Region Overlays
12. [ ] Task 6.2 - Margin Analysis Flags
13. [ ] Task 6.3 - Cross-Sell Matrix

### Week 4: Low Priority
14. [ ] Task 5.2 - Field Day Calendar
15. [ ] Task 5.3 - Agronomist Network
16. [ ] Task 7.1 - Sparkline Charts
17. [ ] Task 7.2 - Live Data Indicators

---

## Sign-Off Criteria

### Technical Verification
```bash
# Run after each phase
npm run test:dashboard  # If tests exist
# OR manual browser check

# Verify Supabase connection
curl https://emgydkspmnaymxpdqpjr.supabase.co/rest/v1/analysis/product_families \
  -H "apikey: YOUR_KEY" | jq '.[] | .product_family'
```

### Business Verification
- [ ] Saxon reviews Customer Actions data accuracy
- [ ] Compare Top 20 products to MYOB reports
- [ ] Verify credit alerts match accounting
- [ ] Check dormant customer list against CRM

---

*To-Do List v1.0 | 26 December 2025*
