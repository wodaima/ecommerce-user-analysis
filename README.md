# E-Commerce User Behavior Analysis

**[中文版 →](README_CN.md)**

**Data-Driven Customer Segmentation & Growth Strategy Using RFM + K-Means Clustering**

> Analyzed ~1M real transaction records from the UCI Online Retail II dataset, delivering a complete pipeline from data cleaning to actionable product strategy recommendations.

---

## 📌 Background

In e-commerce, customer value varies dramatically — a small number of high-value customers often account for the majority of revenue. This project validates that hypothesis through data and produces differentiated operational strategies based on customer segmentation.

**Key Finding: 22% of customers contribute 68% of total revenue.**

---

## 🔬 Analysis Pipeline

```mermaid
graph LR
    A[📥 Raw Data<br/>1.07M transactions] --> B[🧹 Data Cleaning<br/>Missing values · Returns · Outliers]
    B --> C[📊 EDA<br/>Time · Geography · Products · Users]
    C --> D[🏷️ RFM Segmentation<br/>Recency · Frequency · Monetary]
    D --> E[🤖 K-Means Validation<br/>Cross-verify segments]
    E --> F[💡 Strategy<br/>Differentiated action plans]
    F --> G[📊 Dashboard<br/>Interactive visualization]

    style A fill:#1e1b4b,stroke:#818cf8,color:#e2e8f0
    style B fill:#1e1b4b,stroke:#818cf8,color:#e2e8f0
    style C fill:#1e1b4b,stroke:#34d399,color:#e2e8f0
    style D fill:#1e1b4b,stroke:#34d399,color:#e2e8f0
    style E fill:#1e1b4b,stroke:#fbbf24,color:#e2e8f0
    style F fill:#1e1b4b,stroke:#f87171,color:#e2e8f0
    style G fill:#1e1b4b,stroke:#c084fc,color:#e2e8f0
```

---

## 🧠 Analysis thinking chain

### Overview

```mermaid
graph TD
    Q0{"🤔 How different are<br/>customer values?"}
    Q0 --> M["📊 01-02 Data prep<br/>Clean 1.07M records"]
    M --> Q1{"What patterns are<br/>hidden in the data?"}
    Q1 --> E["🔍 02 EDA<br/>Four-dimension exploration"]
    E --> Q2{"How to segment<br/>the customers?"}
    Q2 --> R["🏷️ 03 RFM<br/>Rule-based"]
    Q2 --> K["🤖 04 K-Means<br/>Data-driven"]
    R --> Q3{"Are the segments<br/>reliable?"}
    K --> Q3
    Q3 --> S["💡 05 Strategy<br/>+ Dashboard"]

    style Q0 fill:#6366f1,stroke:#4f46e5,color:#fff
    style Q1 fill:#f97316,stroke:#ea580c,color:#fff
    style Q2 fill:#e74c3c,stroke:#dc2626,color:#fff
    style Q3 fill:#2ecc71,stroke:#16a34a,color:#fff
    style M fill:#ede9fe,stroke:#6366f1,color:#333
    style E fill:#fff7ed,stroke:#f97316,color:#333
    style R fill:#fee2e2,stroke:#e74c3c,color:#333
    style K fill:#dbeafe,stroke:#3498db,color:#333
    style S fill:#dcfce7,stroke:#2ecc71,color:#333
```

### Branch 1 — "Is the data clean?" → Data preparation

```mermaid
graph TD
    ROOT["🧹 1.07M raw records<br/>Ready to use?"]

    ROOT --> LOAD["Data loading<br/>2 sheets merged · 8 fields · 1,067,371 rows"]
    ROOT --> QUALITY["Quality scan"]

    QUALITY --> Q1["Customer ID missing<br/>243,007 rows (22.8%)"]
    QUALITY --> Q2["Return orders<br/>Invoice starts with C · 19,494 rows"]
    QUALITY --> Q3["Outliers<br/>Quantity≤0: 22,950<br/>Price≤0: 6,207"]
    QUALITY --> Q4["Description missing<br/>4,382 rows (0.4%) → keep"]

    ROOT --> DECISION["Cleaning decisions"]
    DECISION --> D1["Drop missing CustomerID<br/>→ cannot segment without it"]
    DECISION --> D2["Drop returns + outliers<br/>→ not part of purchase behavior"]
    DECISION --> D3["Derive Revenue field<br/>= Quantity × Price"]

    ROOT --> RESULT["📋 Result:<br/>805,549 rows retained (75%)<br/>5,878 customers · 36,969 orders · £17.7M"]

    style ROOT fill:#6366f1,stroke:#4f46e5,color:#fff
    style RESULT fill:#fef3c7,stroke:#f39c12,color:#333
```

### Branch 2 — "What patterns are hidden?" → Four-dimension EDA

```mermaid
graph TD
    ROOT["🔍 Four dimensions<br/>of business exploration"]

    ROOT --> TIME["⏰ Time"]
    TIME --> T1["Monthly trends<br/>Revenue · Orders · Active customers"]
    TIME --> T2["Finding: strong seasonality<br/>Sep-Nov surges 40%+<br/>Nov peaks (Christmas season)"]
    TIME --> T3["Implication: start holiday<br/>marketing prep in Q3"]

    ROOT --> GEO["🌍 Geography"]
    GEO --> G1["Top 10 markets<br/>Revenue by country"]
    GEO --> G2["Finding: single-market dominance<br/>UK = 83% revenue · 5,350 customers (91%)<br/>EIRE #2 but only 5 customers"]
    GEO --> G3["Implication: overseas expansion<br/>needs retail vs wholesale segmentation"]

    ROOT --> PROD["📦 Products"]
    PROD --> P1["Top 20 + Pareto analysis"]
    PROD --> P2["Finding: long-tail effect<br/>20% products drive 78% revenue<br/>Near Pareto 80/20"]
    PROD --> P3["Anomaly: PAPER CRAFT<br/>1 buyer · 80K+ units → wholesale"]
    PROD --> P4["Implication: rec engine<br/>should focus on top SKUs"]

    ROOT --> USER["👥 Users"]
    USER --> U1["Frequency · Spend · SKU count distributions"]
    USER --> U2["Finding: power-law distribution<br/>Median spend £899 vs Mean £3,019<br/>Max £608K"]
    USER --> U3["Implication: cannot design for<br/>the average user → need segmentation"]

    style ROOT fill:#f97316,stroke:#ea580c,color:#fff
```

### Branch 3 — "How to segment customers?" → RFM model

```mermaid
graph TD
    ROOT["🏷️ RFM model<br/>Rule-based customer segmentation"]

    ROOT --> CALC["Step 1: compute metrics"]
    CALC --> R["R = Recency<br/>Days since last purchase<br/>Lower is better"]
    CALC --> F["F = Frequency<br/>Unique invoice count<br/>Higher is better"]
    CALC --> M["M = Monetary<br/>Total spend<br/>Higher is better"]

    ROOT --> SCORE["Step 2: quintile scoring"]
    SCORE --> SC1["Each dimension scored 1-5<br/>R inverted — fewer days = higher score"]
    SCORE --> SC2["Frequency has many ties<br/>→ rank(method='first') before qcut"]
    SCORE --> SC3["Combined into RFM string<br/>e.g. '545' = high R, mid-high F, high M"]

    ROOT --> LABEL["Step 3: label mapping"]
    LABEL --> SEG["8 customer segments"]
    SEG --> S1["💎 Loyal high-value<br/>1,300 · 68.4% revenue<br/>R≥4 F≥4 M≥4"]
    SEG --> S2["🌱 High potential<br/>975 · 13.8% revenue<br/>R≥3 F≥3 M≥3"]
    SEG --> S3["🚨 At-risk high-value<br/>227 · 5.7% revenue<br/>R≤2 F≥4 M≥4"]
    SEG --> S4["Other 5 segments<br/>3,376 · 12.1% revenue"]

    ROOT --> KEY["📋 Key finding:<br/>22% of customers contribute 68% revenue<br/>Customer value is highly concentrated"]

    style ROOT fill:#e74c3c,stroke:#dc2626,color:#fff
    style KEY fill:#fef3c7,stroke:#f39c12,color:#333
```

### Branch 4 — "Are the segments reliable?" → K-Means validation

```mermaid
graph TD
    ROOT["🤖 Data-driven validation<br/>of rule-based segments"]

    ROOT --> PREP["Preprocessing<br/>StandardScaler on R/F/M"]

    ROOT --> KSELECT["Choosing K"]
    KSELECT --> ELBOW["Elbow method<br/>K=2 to 10 · compute inertia"]
    KSELECT --> CHOOSE["Elbow at K=4~5<br/>Choose K=5 · comparable to RFM groups"]

    ROOT --> RESULT["Clustering results"]
    RESULT --> C0["Quality active · 383<br/>R=28 F=28.5 M=£13,935"]
    RESULT --> C1["Dormant low-value · 1,917<br/>R=471 F=2.2 M=£756"]
    RESULT --> C2["Super customers · 24<br/>R=22.5 F=119.8 M=£100,927"]
    RESULT --> C3["Extreme VIP · 4<br/>R=3.5 F=212.5 M=£436,836"]
    RESULT --> C4["General active · 3,550<br/>R=75.7 F=5.1 M=£1,912"]

    ROOT --> CROSS["Cross-validation<br/>crosstab + heatmap"]
    CROSS --> X1["✅ Consistent: 87% overlap<br/>on dormant customers"]
    CROSS --> X2["🔍 New: 4 extreme VIPs<br/>avg £430K · likely wholesale<br/>RFM lumped as 'loyal high-value'"]
    CROSS --> X3["🔍 New: 24 super customers<br/>avg £100K · RFM missed the tier gap"]

    ROOT --> CONCLUSION["📋 Conclusion:<br/>RFM → clear rules, good for daily ops<br/>K-Means → hidden patterns + outliers<br/>Best used in combination"]

    style ROOT fill:#3498db,stroke:#2563eb,color:#fff
    style CONCLUSION fill:#fef3c7,stroke:#f39c12,color:#333
```

### Branch 5 — "What should we do?" → Strategy recommendations

```mermaid
graph TD
    ROOT["🎯 Differentiated strategy<br/>by customer segment"]

    ROOT --> PRINCIPLE["Core principle<br/>80% resources on P0-P2<br/>covering 87.9% of revenue"]

    ROOT --> P0["🔴 P0 — Act this week"]
    P0 --> P0A["At-risk high-value · 227<br/>Root cause: once valuable · avg 341 days inactive<br/>Action: 3-email win-back sequence<br/>+ exclusive high-value coupons<br/>+ phone interviews for churn reasons<br/>KPI: 30-day win-back rate > 15%"]

    ROOT --> P1["🟡 P1 — Launch this month"]
    P1 --> P1A["Loyal high-value · 1,300<br/>Root cause: revenue lifeline<br/>Action: VIP tier system<br/>+ 48h early access + personalized recs<br/>+ loyalty points to raise switching cost<br/>KPI: quarterly retention > 95%"]
    P1 --> P1B["High potential · 975<br/>Root cause: largest growth pool<br/>Action: milestone rewards at £5K<br/>+ cross-category recommendations<br/>KPI: high-value conversion > 8%"]

    ROOT --> P2["🟢 P2 — Ongoing"]
    P2 --> P2A["New · 443<br/>→ 7-day onboarding + 14-day 2nd-order discount"]
    P2 --> P2B["Dormant · 1,523<br/>→ low-cost outreach · batch 20% test first"]

    ROOT --> AB["🧪 A/B tests"]
    AB --> AB1["Win-back emails<br/>Control: no action · Treatment: 3-email sequence<br/>Metric: 30-day repurchase · Duration: 4 weeks"]
    AB --> AB2["Milestone incentives<br/>Control: standard promo · Treatment: spend milestones<br/>Metric: 60-day spend change · Duration: 8 weeks"]
    AB --> AB3["2nd-order conversion<br/>Control: no action · Treatment: 10% off within 14 days<br/>Metric: 30-day 2nd purchase rate · Duration: 4 weeks"]

    style ROOT fill:#2ecc71,stroke:#16a34a,color:#fff
    style P0 fill:#fee2e2,stroke:#e74c3c,color:#333
    style P1 fill:#fef3c7,stroke:#f39c12,color:#333
    style P2 fill:#dcfce7,stroke:#2ecc71,color:#333
```

---

## 📈 Key Findings

| Dimension | Finding | Product Implication |
|-----------|---------|-------------------|
| ⏰ Time | Sales surge 40%+ during Sep–Nov (Christmas season), peaking in Nov | Begin holiday marketing prep in Q3 |
| 🌍 Geography | UK accounts for 83% of revenue | Overseas expansion requires retail vs. wholesale segmentation |
| 📦 Products | Top 20% of products drive 78% of revenue | Recommendation engine should focus on top SKUs |
| 👥 Users | Power-law distribution; median spend only £899 | Cannot design strategy around the "average user" |

---

## 🏷️ Customer Segments

RFM analysis segmented 5,878 customers into 8 groups:

| Segment | Count | Revenue Share | Strategy |
|---------|-------|--------------|----------|
| 💎 Loyal High-Value | 1,300 | 68.4% | VIP perks · Personalized recs · Loyalty program |
| 🌱 High Potential | 975 | 13.8% | Milestone rewards · Category expansion · Flash sales |
| 🚨 At-Risk High-Value | 227 | 5.7% | Urgent win-back · Exclusive coupons · Churn interviews |
| 👤 Regular | 1,102 | 4.6% | Standard maintenance |
| 💤 Dormant | 1,523 | 3.8% | Low-cost outreach · Batch testing |
| 👋 New | 443 | 2.2% | Onboarding emails · Second-order incentives |
| 🔄 Frequent Low-Spend | 182 | 0.9% | Cross-sell · AOV uplift |
| ⚠️ At-Risk Regular | 126 | 0.6% | Monitor · Low priority |

---

## 🤖 K-Means Clustering Validation

Applied K-Means (K=5) on standardized RFM features to cross-validate the rule-based segmentation:

- ✅ **Consistent**: 87% of dormant customers were identified by both methods
- 🔍 **New discovery**: K-Means detected 4 "extreme VIPs" (avg. spend £430K, likely wholesale) and 24 "super customers" (avg. £100K) that RFM failed to distinguish
- 💡 **Conclusion**: RFM excels in interpretability for daily operations; K-Means uncovers hidden patterns and extreme outliers. Best used in combination.

---

## 💡 Strategy Prioritization

```
P0 🚨 At-Risk High-Value    →  Win-back this week  (227 · proven high-LTV users)
P1 💎 Loyal High-Value      →  Launch VIP program   (1,300 · revenue lifeline)
P2 🌱 High Potential        →  Milestone incentives (975 · largest growth pool)
P3 👋 New Customers         →  Second-order nudge   (443 · long-term cultivation)
P4 💤 Dormant               →  Low-cost batch test  (1,523 · don't over-invest)

Core principle: Allocate 80% of resources to P0–P2, covering 87.9% of revenue.
```

---

## 🛠️ Tech Stack

| Module | Tools |
|--------|-------|
| Data Processing | Python · pandas · numpy |
| Visualization | matplotlib · seaborn · plotly |
| Machine Learning | scikit-learn (KMeans · StandardScaler · PCA) |
| Dashboard | Streamlit |

---

## 📁 Project Structure

```
ecommerce-user-analysis/
├── notebooks/
│   ├── 01_data_cleaning.ipynb    # Data cleaning & preprocessing
│   ├── 02_eda.ipynb              # Exploratory data analysis
│   ├── 03_rfm_analysis.ipynb     # RFM customer segmentation
│   ├── 04_clustering.ipynb       # K-Means clustering validation
│   └── 05_insights.ipynb         # Product strategy recommendations
├── dashboard/
│   └── app.py                    # Streamlit interactive dashboard
├── data/                         # Data directory (not uploaded)
└── docs/                         # Documentation & screenshots
```

---

## 🚀 Quick Start

```bash
# Clone
git clone https://github.com/wodaima/ecommerce-user-analysis.git
cd ecommerce-user-analysis

# Environment
python -m venv .venv
.venv\Scripts\activate        # Windows
# source .venv/bin/activate   # Mac/Linux

# Dependencies
pip install pandas numpy matplotlib seaborn plotly scikit-learn streamlit openpyxl jupyter

# Data — download from link below and place in data/
# https://archive.ics.uci.edu/dataset/502/online+retail+ii

# Run notebooks
jupyter notebook

# Launch dashboard
cd dashboard
streamlit run app.py
```

---

## 📊 Dashboard Preview

> Screenshots / GIF coming soon

---

## 📝 License

MIT
