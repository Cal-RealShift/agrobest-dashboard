# Dashboard Improvement Plan

**File:** `Dashboard-3-Sales-Actions.html`
**Created:** December 2025
**Status:** Prototype → Production-ready

---

## Current State Assessment

| Tab | Current Score | Issues | Priority |
|-----|---------------|--------|----------|
| Dashboard | 4/10 | Too simple, static KPIs only | HIGH |
| Customer Actions | 5/10 | Lacks detail, no filters, limited history | HIGH |
| Market Intel | 5/10 | Missing external data feeds | MEDIUM |
| Crop Calendar | 4/10 | Products by SKU not family, incomplete | HIGH |
| Customer Map | 3/10 | Non-interactive SVG, no zoom/click | CRITICAL |
| Opportunities | 4/10 | Missing Market-Research data | HIGH |
| Products | 3/10 | Limited products, no cross-sell logic | HIGH |

---

## Tab 1: Dashboard

### Current Problems
- Static KPI cards with no trend indicators
- No interactive elements
- Missing real-time data feel
- No drill-down capability

### Improvements

#### 1.1 Enhanced KPI Cards
```
CURRENT:                          IMPROVED:
┌─────────────┐                  ┌─────────────────────────────────┐
│ YTD Revenue │                  │ YTD Revenue          ▲ 12%     │
│ $3.4M       │       →         │ $3.4M                vs LY     │
└─────────────┘                  │ ▁▂▃▄▅▆▇▆▅▄▃  sparkline       │
                                 │ [Click for breakdown]          │
                                 └─────────────────────────────────┘
```

- Add sparkline charts (last 12 months)
- YoY comparison with trend arrow
- Click-to-drill into detail view
- Pulse animation on critical metrics

#### 1.2 Add Real-Time Sections
| New Section | Data Source | Visual |
|-------------|-------------|--------|
| Today's Orders | Supabase live | Ticker/feed |
| Weather Alert | BOM API | Banner with product triggers |
| This Week's Focus | WF3 output | Priority action cards |
| ENSO Status | `market.weather_outlook` | Status indicator |

#### 1.3 AI Insights Panel
- Move from static text to dynamic insights
- Source from `insights.daily_sales_actions`
- Show confidence scores
- Link to recommended actions

---

## Tab 2: Customer Actions

### Current Problems
- Only shows December gap analysis
- No customer search/filter
- Missing contact details beyond phone
- No interaction history
- Win-back opportunities are placeholder data

### Improvements

#### 2.1 Enhanced Customer Cards
```html
<!-- IMPROVED: Full customer detail -->
<div class="customer-card">
    <div class="customer-header">
        <h3>LIVING TURF - MARRICKVILLE</h3>
        <span class="segment-badge">WHITE-LABEL</span>
        <span class="risk-badge">CREDIT RISK</span>
    </div>
    <div class="customer-metrics">
        <div>LTV: $4.19M</div>
        <div>Avg Order: $85K</div>
        <div>Payment: 45 days</div>
        <div>Last Contact: 3 days ago</div>
    </div>
    <div class="customer-products">
        <h4>Products Purchased (Top 5)</h4>
        <ul>
            <li>MP BRILLIANCE (all sizes): $1.8M</li>
            <li>GREEN BLAST: $645K</li>
            <li>ENHANCE: $439K</li>
        </ul>
    </div>
    <div class="customer-contact">
        <button>📞 02 8594 6000</button>
        <button>✉️ Email</button>
        <button>📋 Add Note</button>
    </div>
    <div class="customer-history">
        <!-- Timeline of interactions -->
    </div>
</div>
```

#### 2.2 Real Dormant Data (from `dormant_customers.csv`)
| Customer | Historical Revenue | Last Order | Days Since | Reason |
|----------|-------------------|------------|------------|--------|
| UPL NEW ZEALAND | $431,595 | Aug 2022 | 1215 | Contract end? |
| MCGREGOR GOURLAY AG SERVICES | $251,934 | Jul 2022 | 1243 | Investigate |
| PROFILL INDUSTRIES | $201,600 | Sep 2021 | 1546 | Business change? |
| BURDEKIN GROWER SERVICES | $183,681 | May 2022 | 1301 | Competitor switch |
| MCGREGOR GOURLAY RURAL | $156,184 | Dec 2023 | 724 | Recent - winnable |
| COTTON GROWER SERVICES WEE WAA | $102,858 | Nov 2020 | 1851 | Long dormant |

**Total Dormant Potential: $1.9M** (actual data)

#### 2.3 Add Filters & Search
- Search by customer name
- Filter by: Segment, State, Priority, Credit Status
- Sort by: Gap $, Last Contact, Revenue
- Export to CSV

#### 2.4 Customer Timeline
- Show last 12 months of orders
- Contact history from CRM
- Credit events
- Product purchase patterns

---

## Tab 3: Market Intel

### Current Problems
- ENSO framework is static
- No live commodity prices
- Weather data not connected
- Missing industry news

### Improvements

#### 3.1 Live Data Feeds
| Source | Data | Update Frequency |
|--------|------|------------------|
| BOM | ENSO status, SOI | Weekly (WF2) |
| Cotlook | Cotton A Index | Daily (WF1) |
| QSL | Sugar pool price | Daily |
| ABARES | Water trade, outlook | Monthly |

#### 3.2 Enhanced Weather Intelligence
```
┌─────────────────────────────────────────────────────────────────┐
│ WEATHER INTELLIGENCE                    Updated: 26 Dec 6am    │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ENSO Status: ◉ NEUTRAL (transitioning La Nina)                │
│  SOI: +8.2 (positive = La Nina tendency)                       │
│                                                                 │
│  ┌─────────────┬─────────────┬─────────────┬─────────────┐     │
│  │ DARLING     │ BUNDABERG   │ RIVERLAND   │ GIPPSLAND   │     │
│  │ DOWNS       │             │             │             │     │
│  ├─────────────┼─────────────┼─────────────┼─────────────┤     │
│  │ Rain: +20%  │ Rain: +10%  │ Rain: -10%  │ Rain: +15%  │     │
│  │ Temp: +1°C  │ Temp: +2°C  │ Temp: +0.5° │ Temp: +0.5° │     │
│  │ ▲ PHOZSTART │ ▲ FOLIAR    │ ▼ STARTER   │ ▲ PASTURE   │     │
│  └─────────────┴─────────────┴─────────────┴─────────────┘     │
│                                                                 │
│  PRODUCT IMPLICATIONS:                                         │
│  • La Nina tendency = GOOD autumn break expected (Apr-May)     │
│  • Push PHOZSTART pre-orders NOW for April delivery            │
│  • Darling Downs cotton boll fill = EC KOMPLETE priority       │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

#### 3.3 Commodity Price Tracker
| Commodity | Current | Trend | Implication | Source |
|-----------|---------|-------|-------------|--------|
| Cotton (Cotlook A) | $580/bale | ↑ 5% | Growers investing → push inputs | WF1 |
| Sugar (ICE #11) | $485/t | ↓ 3% | Steady, no urgency | WF1 |
| Wheat (CBOT) | $278/t | ↔ | Neutral | WF1 |
| Water (MDB) | $450/ML | ↑ 12% | Irrigators active | ABARES |

#### 3.4 Industry News Feed
Source from WF5 (future):
- Cotton Australia RSS
- CANEGROWERS news
- GRDC updates
- Farm Weekly

---

## Tab 4: Crop Calendar

### Critical Fix: Product Family Aggregation

**Current Problem:** Shows "ENVY 1000L" as separate from "ENVY 20L"
**Solution:** Aggregate all product sizes into families

#### 4.1 Product Family Mapping
| Family | Sizes | Combined Revenue |
|--------|-------|------------------|
| PHOZSTART | ZZ 1000L, 20L, BULK | $2.8M |
| EC KOMPLETE | 1000L, 20L | $2.3M |
| POPSTART | NP BULK, PZ 1000L, AP | $2.5M |
| ENVY | 1000L, 20L | $760K |
| MP BRILLIANCE | 1000L, 20L, QT | $1.8M |
| PHOZGUARD 620 | 1000L, 20L | $1.9M |
| GREEN BLAST | 1000L, 22 20L | $900K |
| CHICKPEA | 1000L | $430K |
| CARBOCAL | 1000L | $473K |
| KOMPLETE | 1000L, 20L | $1.1M |
| AGRODEX K | K45, K35 | $350K |
| BROADHECTARE | ZN, MN, range | $773K |

#### 4.2 Improved Seasonality Table
```
┌────────────────────────────────────────────────────────────────────────────┐
│ PRODUCT FAMILY SEASONALITY (All Sizes Combined)                            │
├───────────────┬─────────┬────────────────────────────────────┬─────────────┤
│ Product       │ Revenue │ J  F  M  A  M  J  J  A  S  O  N  D │ Peak Window │
├───────────────┼─────────┼────────────────────────────────────┼─────────────┤
│ EC KOMPLETE   │ $2.3M   │ ██ █░ ░░ ░░ ░░ ░░ ░░ ░░ ░░ █░ ██ ██│ Nov-Jan     │
│ POPSTART      │ $2.5M   │ ░░ ░░ ░░ █░ ██ ░░ ░░ ░░ ██ ██ ██ ░░│ Apr+Sep-Nov │
│ PHOZSTART     │ $2.8M   │ ░░ ░░ ░░ █░ █░ ░░ ░░ ░░ ██ ██ ██ ░░│ Sep-Nov     │
│ MP BRILLIANCE │ $1.8M   │ ██ ██ ██ ██ ██ ██ ██ ██ ██ ██ ██ ██│ Year-round  │
│ PHOZGUARD     │ $1.9M   │ ██ █░ ░░ ░░ ░░ ░░ ░░ ░░ ░░ ██ ██ ██│ Oct-Jan     │
│ KOMPLETE      │ $1.1M   │ ██ █░ ░░ ░░ ░░ ░░ ░░ ░░ ░░ ██ ██ ██│ Oct-Jan     │
│ GREEN BLAST   │ $900K   │ ██ ██ ██ ██ ██ ██ ██ ██ ██ ██ ██ ██│ Year-round  │
│ ENVY          │ $760K   │ ░░ ░░ ░░ ░░ █░ █░ ██ ██ ██ ██ ░░ ░░│ Jul-Oct     │
│ BROADHECTARE  │ $773K   │ ░░ ░░ ░░ ░░ ░░ ██ ██ ██ ░░ ░░ ░░ ░░│ Jun-Aug     │
│ CARBOCAL      │ $473K   │ ░░ ░░ ░░ ░░ ░░ ██ ██ ██ ░░ ░░ ░░ ░░│ Jun-Aug     │
│ CHICKPEA      │ $430K   │ ░░ ░░ ░░ ░░ ░░ ██ ██ ██ ░░ ░░ ░░ ░░│ Jun-Aug     │
│ AGRODEX K     │ $350K   │ ░░ ░░ ░░ ░░ ░░ ░░ ░░ ░░ ░░ ██ ██ ██│ Oct-Dec     │
└───────────────┴─────────┴────────────────────────────────────┴─────────────┘
Legend: ██ Peak (>15%) | █░ Active (8-15%) | ░░ Low (<8%)
```

#### 4.3 Add Crop-to-Product Mapping
| Crop | Application Window | Products | Weather Trigger |
|------|-------------------|----------|-----------------|
| Cotton | Nov-Mar | EC KOMPLETE, KOMPLETE, AGRODEX K | Irrigated - stable |
| Cereals (Wheat/Barley) | Apr-Jun (sowing) | PHOZSTART, POPSTART | Autumn break >25mm |
| Cereals (Wheat/Barley) | Sep-Nov (spring) | POPSTART PZ, foliars | Spring break |
| Turf | Year-round | MP BRILLIANCE, GREEN BLAST, ENVY | Heat = stress products |
| Chickpea | Jun-Aug | CHICKPEA specialty | Dry season NT |
| Avocado | Year-round | CARBOCAL, K products | Fruit development |
| Sugarcane | Oct-Apr | N+K blends, CARBOCAL | Wet season |

#### 4.4 Monthly Action Matrix
```
┌──────────────────────────────────────────────────────────────────┐
│ DECEMBER ACTION MATRIX                                           │
├──────────────────────────────────────────────────────────────────┤
│                                                                  │
│ SELL NOW:                      PRE-SELL FOR NEXT MONTH:         │
│ • EC KOMPLETE (cotton boll)    • CHICKPEA (Jan delivery)        │
│ • MP BRILLIANCE (peak heat)    • K foliars (cotton stress)      │
│ • PHOZGUARD (disease season)   • Trace elements (Jan timing)    │
│                                                                  │
│ SEASONAL TRANSITION:           WATCH FOR:                       │
│ • Cotton → boll fill peak      • Heat stress events             │
│ • Turf → maximum demand        • Irrigation water pricing       │
│ • Hort → fruit development     • La Nina rain events            │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

---

## Tab 5: Customer Map

### Current Problems
- Static SVG - no interactivity
- Hardcoded positions
- No zoom/pan
- No click-to-detail
- No crop overlay
- Inaccurate geographic placement

### Solution: Interactive Map with Leaflet.js

#### 5.1 Technology Stack
```html
<!-- Replace static SVG with Leaflet -->
<link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
```

#### 5.2 Features Required
| Feature | Description |
|---------|-------------|
| Base map | OpenStreetMap or satellite tiles |
| Customer markers | Clickable pins with popup detail |
| Whitespace markers | Different icon/color for opportunities |
| Crop overlay | Toggle crop regions (from DEA data) |
| Heat map | Revenue density visualization |
| Zoom controls | Standard map controls |
| Search | Find customer by name |
| Filter | By segment, by crop, by revenue |

#### 5.3 Map Layers
```javascript
// Layer structure
const layers = {
    customers: L.layerGroup(),      // Current customers
    whitespace: L.layerGroup(),     // Opportunity regions
    crops: {
        cotton: L.layerGroup(),     // Cotton regions
        turf: L.layerGroup(),       // Urban/turf areas
        horticulture: L.layerGroup(),
        broadacre: L.layerGroup()
    },
    weather: L.layerGroup()         // Weather overlays
};
```

#### 5.4 Customer Popup Template
```html
<div class="map-popup">
    <h3>LIVING TURF - MARRICKVILLE</h3>
    <div class="popup-metrics">
        <div>Revenue: $4.19M</div>
        <div>Segment: White-label</div>
        <div>Products: MP Brilliance, Green Blast</div>
    </div>
    <div class="popup-actions">
        <button onclick="viewCustomer('LIVING_TURF')">View Details</button>
        <button onclick="callCustomer('0285946000')">Call</button>
    </div>
</div>
```

#### 5.5 Crop Region Data Sources
| Source | Data | Access |
|--------|------|--------|
| DEA Land Cover | Crop types | Free API |
| ABS Census | Agricultural regions | CSV download |
| QLD Crop Monitoring | Broadacre | QLD Gov portal |

---

## Tab 6: Opportunities

### Current Problems
- Only shows 6 regions
- Missing detailed TAM data
- No agronomist network info
- No field day calendar
- Not using Market-Research folder data

### Improvements from Market-Research Data

#### 6.1 Expanded Whitespace Matrix (from `02-Crop-Region-Matrix-Template.md`)

| Region | Crop | Hectares | Farms | Est. TAM | AgroBest Status | Score |
|--------|------|----------|-------|----------|-----------------|-------|
| Bundaberg | Avocado | 8,500 | 450 | $6.4M | No presence | 78 |
| Bundaberg | Macadamia | 12,000 | 800 | $7.8M | No presence | 82 |
| Riverland | Citrus | 18,000 | 600 | $9.9M | No presence | 75 |
| Riverland | Almonds | 15,000 | 200 | $8.5M | No presence | 72 |
| Gippsland | Dairy/Pasture | 50,000+ | 1,200 | $5.0M | No presence | 68 |
| Atherton | Tropical hort | 5,000 | 300 | $2.5M | 1 customer | 62 |
| Sunraysia | Grapes | 20,000 | 800 | $4.5M | No presence | 58 |
| Perth Metro | Turf | 10,000 | 200 | $12M | No presence | 45 |
| Liverpool Plains | Cotton | 80,000 | 150 | $8M | Adjacent | 70 |
| Burdekin | Sugarcane | 90,000 | 600 | $3M | 1 dormant | 55 |
| Hunter | Viticulture | 8,000 | 300 | $2M | No presence | 48 |

**Total Addressable Market: $70M+**

#### 6.2 Agronomist Network (from `10-Enhanced-Framework.md`)
| Network | Reach | Strategy |
|---------|-------|----------|
| Crop Consultants Australia | 500+ members | Sponsor events, training |
| Elders Agronomist Network | National | Leverage Elders-Primac |
| The Australian Agronomist | 3,500 subscribers | Advertise, contribute content |
| Independent consultants | 20+ key influencers | Technical training program |

#### 6.3 Field Day Calendar (from `11-Novel-Data-Sources.md`)
| Event | Date | Location | Target Crop | Priority |
|-------|------|----------|-------------|----------|
| Farm World | Mar 27-29, 2025 | Lardner, VIC | Mixed | HIGH |
| Bundaberg Fruit & Veg | TBC | Bundaberg, QLD | Hort | HIGH |
| Henty Field Days | Sep 23-25, 2025 | Henty, NSW | Broadacre | HIGH |
| Elmore Field Days | Oct 7-9, 2025 | Elmore, VIC | Mixed | MEDIUM |
| Cotton Conference | Various | Cotton regions | Cotton | HIGH |

#### 6.4 Data Sources Panel (from `11-Novel-Data-Sources.md`)
```
┌─────────────────────────────────────────────────────────────────┐
│ MARKET INTELLIGENCE SOURCES                                     │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│ SATELLITE DATA:                                                 │
│ ○ DEA Land Cover (maps.dea.ga.gov.au) .............. FREE      │
│ ○ QLD Crop Monitoring ............................ FREE        │
│ ○ CSIRO ePaddocks (1.7M paddocks) ................ RESEARCH    │
│                                                                 │
│ FARMER DATABASES:                                               │
│ ○ Farmers Australia (67K contacts) ............... $0.10-0.50/r│
│ ○ Baron Strategic Services ...................... QUOTE        │
│ ○ KG2 (CATI research) ........................... PROJECT      │
│                                                                 │
│ WATER DATA:                                                     │
│ ○ ABARES MDB Water Dataset ...................... FREE         │
│ ○ State Water Registers ......................... FREE         │
│                                                                 │
│ INDUSTRY:                                                       │
│ ○ Hort Innovation Statistics .................... FREE         │
│ ○ ABS Agricultural Census ....................... FREE         │
│ ○ GRDC Regional Updates ......................... FREE         │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

#### 6.5 Investment Tier Recommendation (from `12-Strategic-Recommendation.md`)
| Tier | Budget | Expected Return | ROI |
|------|--------|-----------------|-----|
| **Tier 1: Minimum** | $5-8K | 1-2 customers, $100-150K | 15-20x |
| **Tier 2: Balanced** | $12-18K | 3-5 customers, $250-400K | 18-25x |
| **Tier 3: Accelerated** | $25-35K | 5-8 customers, $400-600K | 15-20x |

---

## Tab 7: Products

### Current Problems
- Only 5 products shown
- No margin data
- No cross-sell recommendations
- Missing product-customer matrix
- No competitive positioning

### Improvements

#### 7.1 Complete Product Catalog (from `product_crop_mapping.csv`)
| Category | Products | Total Revenue | Margin Range |
|----------|----------|---------------|--------------|
| Starters | PHOZSTART, POPSTART range | $5.3M | 8-35% |
| Cotton Nutrition | EC KOMPLETE, KOMPLETE, CHICKPEA | $3.9M | 36%+ |
| Crop Protection | ENVY, PHOZGUARD | $2.7M | 34-50% |
| Turf (White-label) | MP BRILLIANCE, GREEN BLAST | $2.7M | Manufacturing |
| Trace Elements | BROADHECTARE, AGRODEX | $1.1M | 40-60% |
| Adjuvants | SPRAYTECH OIL, FASTUP, NATRASOAP | $800K | 60-77% |
| Specialty | CARBOCAL, FISH EMULSION, SILAGE | $700K | 50-63% |

#### 7.2 Product-Customer Concentration Matrix
| Product Family | #1 Customer | % of Product | Risk Level |
|----------------|-------------|--------------|------------|
| EC KOMPLETE | Advanced Nutrients | 100% | CRITICAL |
| KOMPLETE | Advanced Nutrients | 100% | CRITICAL |
| CHICKPEA | Advanced Nutrients | 100% | CRITICAL |
| MP BRILLIANCE | Living Turf | 100% | HIGH |
| GREEN BLAST | Oasis Pacific | 100% | HIGH |
| POPSTART | Multiple | Diversified | LOW |
| PHOZGUARD | Multiple | Diversified | LOW |

#### 7.3 Cross-Sell Matrix
```
┌────────────────────────────────────────────────────────────────┐
│ IF CUSTOMER BUYS...      RECOMMEND...           REASON         │
├────────────────────────────────────────────────────────────────┤
│ PHOZSTART               POPSTART PZ            Same application│
│ EC KOMPLETE             AGRODEX K45            Boll fill combo │
│ ENVY                    CARBOCAL               Stress stack    │
│ MP BRILLIANCE           GREEN BLAST            Turf system     │
│ PHOZGUARD               SPRAYTECH OIL          Tank mix        │
│ CARBOCAL                K products             Hort nutrition  │
│ BROADHECTARE ZN         BROADHECTARE MN        Complete trace  │
└────────────────────────────────────────────────────────────────┘
```

#### 7.4 Margin Analysis (Flag Low Margins)
| Product | Revenue | Margin | Flag |
|---------|---------|--------|------|
| PHOZSTART ZZ 1000L | $2.16M | 8% | REVIEW PRICING |
| AN ADVANCED GRO PHOZ | $401K | 29% | OK |
| PHOZGUARD 620 1000L | $1.09M | 34% | OK |
| EC KOMPLETE 1000L | $1.99M | 36% | OK |
| ENVY 1000L | $1.42M | 50% | GOOD |
| CARBOCAL 1000L | $473K | 63% | EXCELLENT |
| NATRASOAP 20L | $486K | 77% | EXCELLENT |

---

## Implementation Priority

### Phase 1: Quick Wins (1-2 days)
| Task | Tab | Effort | Impact |
|------|-----|--------|--------|
| Product family aggregation | Crop Calendar | 4 hrs | HIGH |
| Real dormant customer data | Customer Actions | 2 hrs | HIGH |
| Add all Market-Research opportunities | Opportunities | 4 hrs | HIGH |
| Complete product catalog | Products | 3 hrs | MEDIUM |

### Phase 2: Enhanced Data (3-5 days)
| Task | Tab | Effort | Impact |
|------|-----|--------|--------|
| Interactive map (Leaflet.js) | Customer Map | 8 hrs | CRITICAL |
| Customer detail expansion | Customer Actions | 6 hrs | HIGH |
| Cross-sell matrix | Products | 4 hrs | MEDIUM |
| Weather data integration | Market Intel | 6 hrs | HIGH |

### Phase 3: Live Data (1-2 weeks)
| Task | Tab | Effort | Impact |
|------|-----|--------|--------|
| n8n workflow connection | All | 16 hrs | HIGH |
| Commodity price feeds | Market Intel | 8 hrs | MEDIUM |
| BOM weather integration | Market Intel | 8 hrs | MEDIUM |
| Customer timeline from CRM | Customer Actions | 8 hrs | HIGH |

---

## Technical Requirements

### JavaScript Libraries
```html
<!-- Add to <head> -->
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
<script src="https://unpkg.com/leaflet.markercluster@1.4.1/dist/leaflet.markercluster.js"></script>
```

### Data Sources Required
| Source | Table/File | Purpose |
|--------|------------|---------|
| Supabase | `dim.customer_segments` | Customer detail |
| Supabase | `market.commodity_prices` | Live prices (WF1) |
| Supabase | `market.weather_outlook` | Weather (WF2) |
| Supabase | `insights.daily_sales_actions` | AI actions (WF3) |
| CSV | `dormant_customers.csv` | Win-back list |
| CSV | `product_crop_mapping.csv` | Product-crop links |
| API | BOM ENSO | Weather status |
| API | DEA Maps | Crop layers |

### CSS Enhancements
```css
/* Add interactivity styles */
.clickable { cursor: pointer; transition: all 0.2s; }
.clickable:hover { transform: translateY(-2px); box-shadow: 0 4px 12px rgba(0,0,0,0.3); }
.pulse { animation: pulse 2s infinite; }
@keyframes pulse { 0%, 100% { opacity: 1; } 50% { opacity: 0.6; } }
```

---

## Success Metrics

| Metric | Current | Target |
|--------|---------|--------|
| Tab completeness | 40% | 90% |
| Interactive elements | 2 | 15+ |
| Data freshness | Static | Daily updates |
| Products displayed | 5 | 30+ families |
| Opportunities listed | 6 | 15+ |
| Customer details | Basic | Full profile |
| Map functionality | None | Full interactive |

---

*Improvement Plan v1.0 | December 2025*
