# 📊 Marketing Analyst Interview — Real Home Assignment (Data-Driven Notes)

# 🐍 Python Analysis + 📊 Power BI Dashboard

The campaign performance was analyzed using **Python** (for data cleaning, KPI calculation, and segment diagnostics), and the **most actionable insights are presented in this Power BI dashboard**:

👉 https://app.powerbi.com/view?r=eyJrIjoiYmIwZTZlMTYtMmZmZC00YTlhLTkyNjgtNTkxZWU1ZTM5OTUxIiwidCI6IjE2ZGVlZGU4LTc5ZTktNGUzMy05OWU2LTlkOGQzOTQyZDc5NiIsImMiOjEwfQ%3D%3D


---

## 0) 📂 Dataset & Source

- **Notion source page:**  
  https://ruby-dryosaurus-4a2.notion.site/Marketing-Analyst-Interview-Real-Home-Assignment-6afdf1a0a6744d7bac44e138e47000d3


---

# 1) 🧹 Data Understanding & Cleaning

## 🧾 Dataset structure (Python-verified)
- **Main data:** `101,639 rows × 10 columns`
- **Geo mapping:** `18,923 rows × 4 columns`
- After merge (`Hotel_ID` join): **101,639 rows × 13 columns**

## 🧩 Key fields (what each column means)
### ⏱ Time / Entity
- **Week**: weekly reporting key (e.g., `2023/36`, `2024/08`)
- **Hotel_ID**: unique hotel identifier
- **Device**: device segment (Desktop / Mobile / Tablet)

### 📈 Marketing performance
- **Clicks**: traffic volume
- **Bookings**: conversions (final outcome)
- **Revenue**: attributed revenue
- **Cost**: ad spend
- **CPC**: cost per click (provided)
- **Click Value**: value attributed per click (provided)
- **Average Booking**: average value per booking (provided)

### 🌍 Geo fields (from mapping)
- **Country**, **Region**, **Super Region**

## ✅ Data quality checks & issues noticed (Python-verified)
- **Missing values**
  - **Average Booking** has **99,648 missing** → this is expected because many rows have **Bookings = 0**, so average booking value is undefined.
  - **Hotel_ID** has **62 missing** → these rows cannot be mapped/attributed to a hotel and should be excluded from hotel-level optimization.
  - Geo fields missing after join: **Country/Region/Super Region each 299 missing** → hotels not found in mapping or missing `Hotel_ID`.

- **Outliers / anomalies (important for optimization)**
  - Many hotel-week-device rows have **spend but zero bookings/revenue** (common in marketing data).  
  - At **hotel aggregated level**, there are high-spend hotels with **very low ROAS**, which are true inefficiencies (see section 2.4).

## 🧠 Assumptions (stated clearly)
- **ROAS = Revenue / Cost**, **Profit = Revenue − Cost**
- **CVR = Bookings / Clicks**, **CPA = Cost / Bookings**
- For ratios, **division by zero** is treated as `NaN` (not forced to 0), to avoid misleading metrics.

---

# 2) 🔍 Exploratory Analysis (Python-driven insights)

## ⭐ 2.1 Overall performance (all data)
From Python aggregation:

- 💰 **Total Revenue:** **67,983.3**
- 💸 **Total Cost:** **68,023.5**
- 💵 **Total Profit:** **-40.2** *(near break-even)*
- 📈 **ROAS:** **0.999**
- 🖱 **Clicks:** **164,925**
- 📦 **Bookings:** **2,120**
- 🎯 **CVR:** **1.29%**
- 🧾 **CPA:** **32.09** per booking

✅ **Interpretation:** The portfolio-wide result is **basically break-even**, but performance varies strongly across segments (device/geo/hotel).

---

## 🏆 2.2 Which devices perform best?
Python segment summary:

### 📱 Device KPI table (aggregated)
- **Desktop**
  - **ROAS 1.035**, **Profit +1,746.5**
  - **CVR 1.45%**, **CPA 30.34**
  - Largest scale: **113,749 clicks / 1,648 bookings**

- **Mobile**
  - **ROAS 1.146 (highest)**, **Profit +451.3**
  - **CVR 0.84%**, **CPA 25.72**
  - Smaller scale: **14,293 clicks / 120 bookings**

- **Tablet**
  - **ROAS 0.850**, **Profit -2,238.0 (worst)**
  - **CPA 42.45 (highest)**

✅ **Conclusion:**  
- **Best efficiency:** **Mobile** (highest **ROAS**)  
- **Best overall contributor:** **Desktop** (largest profit + scale)  
- **Underperformer:** **Tablet** (low ROAS + negative profit)

---

## 🌍 2.3 Which regions perform best?
Python Super Region results (aggregated):

- 🥇 **EastEurope:** **ROAS 1.269**, **Profit +931.0**
- 🥈 **NorthAmerica:** **ROAS 1.069**, **Profit +495.8** *(but **CPA 61.63** → expensive bookings)*
- 🥉 **WestEurope:** **ROAS 1.008**, **Profit +394.0** *(largest scale: **121,100 clicks / 1,682 bookings**)*

🚨 Underperforming regions:
- **MENA:** **ROAS 0.699**, **Profit -1,079.7**
- **Asia:** **ROAS 0.706**, **Profit -852.8**
- **Oceania:** **ROAS 0.773**, **Profit -104.5**

✅ **Conclusion:**  
- **Scale + stability:** WestEurope (near break-even but huge volume)  
- **Best ROI growth candidate:** EastEurope  
- **Budget caution:** MENA / Asia (structural inefficiency)

---

## 🏨 2.4 Which hotels perform best (and where are inefficiencies)?
To avoid “noise” from tiny-spend hotels, I filtered to **hotels with Cost ≥ 100**:

- **High-spend hotels:** **43**
- **Losing hotels (ROAS < 1):** **26**
- These losing hotels account for **66.4% of high-spend Cost**

✅ **This is the key efficiency issue:**  
Most meaningful spend is concentrated in hotels that do **not** return revenue efficiently.

### 🥇 Top profit hotels (Cost ≥ 100)
Examples (profit leaders):
- **Hotel 18175 (US):** Profit **+593.1**, ROAS **4.33**
- **Hotel 18455 (US):** Profit **+520.1**, ROAS **3.25**
- **Hotel 7472 (UK):** Profit **+463.4**, ROAS **4.27**, **CVR 3.55%**
- **Hotel 16020 (NL):** Profit **+275.4**, ROAS **2.10**, **CPA 10.44** *(excellent cost efficiency)*

### 🚨 Worst efficiency hotels (Cost ≥ 100)
Many worst hotels show **spend with zero revenue**:
- **Hotel 6096 (Finland):** Cost **254.5**, Revenue **0**, ROAS **0**
- **Hotel 10852 (Italy):** Cost **111.3**, Revenue **0**, ROAS **0**
- **Hotel 59 (UAE):** Cost **112.9**, Revenue **0**, ROAS **0**

✅ **Conclusion:**  
The biggest optimization lever is **hotel-level budget reallocation**: scale top-ROAS hotels and aggressively fix/pause zero-revenue spend.

---

## 🔗 2.5 Relationship between Cost, Clicks, Bookings, Revenue
### 📌 Weekly-level correlation (26 weeks)
- **Cost ↔ Clicks:** **0.98** (spend drives traffic strongly)
- **Bookings ↔ Revenue:** **0.91** (bookings drive value strongly)
- **Cost ↔ Revenue:** **0.74** (spend increases revenue, but not perfectly)

### 📌 Hotel-level correlation (high-spend hotels only)
- **Cost ↔ Bookings:** **0.11** *(weak!)*
- **Cost ↔ Revenue:** **0.25** *(weak)*
- **Revenue ↔ Profit:** **0.84** *(strong)*

✅ **Conclusion:**  
At a hotel level, more spend does **not reliably buy** more bookings/revenue → this indicates **allocation inefficiency** and inconsistent conversion performance across hotels.

---

## 🕒 2.6 Patterns over time (by week)
- Total weeks observed: **26** (from **2023/36 → 2024/08**)
- **Average weekly ROAS:** **1.026**
- **ROAS volatility:** std **0.286**

### 🟢 Best week
- **2023/36:** ROAS **1.94**, Profit **+2,102.1**, CPA **21.10**

### 🔴 Worst week
- **2023/45:** ROAS **0.54**, Profit **-1,442.7**, CPA **46.47**

✅ **Conclusion:**  
Performance is **volatile week-to-week**, so decisions should be based on **rolling trends** and **consistent segment performance**, not single-week spikes.

---

# 3) 📊 Visualization (what I built & how it supports decisions)

✅ I delivered an interactive **Power BI dashboard** (slicers + KPI cards + trends + Top-N charts).  
Key visuals include:

- 📌 **KPI Cards:** Revenue / Bookings / Cost / ROAS  
- 🍩 **Revenue by Device**  
- 📉 **Profit Trend by Week**  
- 📈 **ROAS Trend by Week**  
- 🧱 **Top Countries by Revenue (Top 8)**  
- 🫧 **Cost vs Revenue by Hotel** *(inefficiency detection)*

🎯 **Why this works for a Marketing Director**
- **Time to See:** KPI cards show health instantly  
- **Time to Understand:** trends explain stability/volatility  
- **Time to Act:** hotel scatter identifies where to cut or scale budget

---

# 4) ✅ Recommendations (actionable, business-first)

## 1) 🧯 Cut waste: create a “Hotel Inefficiency Watchlist”
- Weekly flag hotels with **Cost ≥ 100** and **ROAS < 1**
- Python shows **26/43** high-spend hotels are losing, consuming **66.4%** of high-spend budget  
✅ **Impact:** fastest ROAS improvement by removing structural loss-makers

## 2) 🚀 Reallocate to proven winners (scale what works)
- Scale **Desktop** (largest profit contributor)  
- Test-scale **Mobile** (highest ROAS but low volume)  
- Reduce **Tablet** (ROAS 0.85, Profit -2,238)
✅ **Impact:** better growth with controlled risk

## 3) 🌍 Geo strategy: shift budget toward high-ROAS regions
- Invest more in **EastEurope (ROAS 1.27)**  
- Maintain but optimize **WestEurope** (largest scale; near break-even)  
- Tighten or restructure **MENA / Asia** (ROAS ~0.70; large negative profit)
✅ **Impact:** improve profitability without reducing total spend

## 4) 🧪 Stabilize volatility with a “baseline + test budget” structure
- Separate campaigns into:
  - **Baseline** (stable segments/hotels)
  - **Test** (new hotels/regions/devices) with budget caps
✅ **Impact:** avoids repeating “worst week” patterns (e.g., ROAS 0.54 in 2023/45)

## 5) 🔍 Audit tracking where spend generates zero revenue
- Several high-spend hotels show **Revenue = 0** (ROAS 0)  
- Validate tracking, attribution window, feed/inventory issues
✅ **Impact:** prevents spending based on broken measurement

---

## 🧾 Executive Summary (1 sentence)
**Overall ROAS is near break-even (0.999), but performance is highly uneven: Desktop drives most profit, Mobile is most efficient, Tablet loses money, and a majority of high-spend hotel budget sits in ROAS<1 hotels—so next quarter’s fastest win is reallocating hotel budgets toward proven winners and high-ROAS regions while cutting zero-revenue spend.**

