# 电商用户行为分析

**[English →](README.md)**

**从数据到决策：基于 RFM 模型与 K-Means 聚类的用户分层与增长策略分析**

> 使用 UCI Online Retail II 数据集（107万条真实交易记录），完成从数据清洗到可落地产品策略的完整分析闭环。

---

## 📌 项目背景

在电商场景中，不同用户的价值差异巨大——少数高价值客户往往贡献绝大部分收入。本项目通过数据分析验证了这一判断，并基于用户分层产出了差异化的运营策略建议。

**核心发现：22% 的用户贡献了 68% 的收入。**

---

## 🔬 分析流程

```mermaid
graph LR
    A[📥 原始数据<br/>107万条交易记录] --> B[🧹 数据清洗<br/>处理缺失值/退货/异常]
    B --> C[📊 探索性分析 EDA<br/>时间·地域·商品·用户]
    C --> D[🏷️ RFM 用户分层<br/>Recency·Frequency·Monetary]
    D --> E[🤖 K-Means 聚类验证<br/>交叉验证分层结果]
    E --> F[💡 产品策略建议<br/>差异化运营方案]
    F --> G[📊 Dashboard<br/>交互式数据看板]

    style A fill:#1e1b4b,stroke:#818cf8,color:#e2e8f0
    style B fill:#1e1b4b,stroke:#818cf8,color:#e2e8f0
    style C fill:#1e1b4b,stroke:#34d399,color:#e2e8f0
    style D fill:#1e1b4b,stroke:#34d399,color:#e2e8f0
    style E fill:#1e1b4b,stroke:#fbbf24,color:#e2e8f0
    style F fill:#1e1b4b,stroke:#f87171,color:#e2e8f0
    style G fill:#1e1b4b,stroke:#c084fc,color:#e2e8f0
```

---

## 🧠 分析思维链与思维树

### 总览

```mermaid
graph TD
    Q0{"🤔 用户价值差异有多大？"}
    Q0 --> M["📊 01-02 数据准备<br/>清洗107万条记录"]
    M --> Q1{"数据里藏着什么规律？"}
    Q1 --> E["🔍 02 探索性分析<br/>四维度拆解"]
    E --> Q2{"怎么给用户分群？"}
    Q2 --> R["🏷️ 03 RFM分层<br/>规则驱动"]
    Q2 --> K["🤖 04 K-Means聚类<br/>数据驱动"]
    R --> Q3{"分群结果可信吗？"}
    K --> Q3
    Q3 --> S["💡 05 策略建议<br/>+ Dashboard"]

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

### 分支一 — "数据干净吗？" → 数据准备

```mermaid
graph TD
    ROOT["🧹 107万条原始数据<br/>能直接用吗？"]

    ROOT --> LOAD["数据加载<br/>2个Sheet合并 · 8字段 · 1,067,371行"]
    ROOT --> QUALITY["数据质量扫描"]

    QUALITY --> Q1["Customer ID缺失<br/>243,007行（22.8%）"]
    QUALITY --> Q2["退货订单<br/>Invoice以C开头 · 19,494行"]
    QUALITY --> Q3["异常值<br/>Quantity≤0: 22,950行<br/>Price≤0: 6,207行"]
    QUALITY --> Q4["Description缺失<br/>4,382行（0.4%）→ 保留"]

    ROOT --> DECISION["清洗决策"]
    DECISION --> D1["删除无CustomerID<br/>→ 无法做用户分层"]
    DECISION --> D2["删除退货+异常值<br/>→ 不纳入消费分析"]
    DECISION --> D3["派生Revenue字段<br/>= Quantity × Price"]

    ROOT --> RESULT["📋 结果：<br/>保留805,549行（75%）<br/>5,878客户 · 36,969订单 · £17.7M收入"]

    style ROOT fill:#6366f1,stroke:#4f46e5,color:#fff
    style RESULT fill:#fef3c7,stroke:#f39c12,color:#333
```

### 分支二 — "数据里藏着什么规律？" → 四维度EDA

```mermaid
graph TD
    ROOT["🔍 四个维度拆解业务"]

    ROOT --> TIME["⏰ 时间维度"]
    TIME --> T1["月度趋势<br/>收入 · 订单数 · 活跃客户数"]
    TIME --> T2["发现：强季节性<br/>每年9-11月攀升40%+<br/>11月达峰值（圣诞采购季）"]
    TIME --> T3["启示：Q3开始备战营销<br/>12月数据截止月初——非真实下降"]

    ROOT --> GEO["🌍 地域维度"]
    GEO --> G1["Top 10市场<br/>按国家聚合收入排序"]
    GEO --> G2["发现：单一市场主导<br/>UK占83%收入 · 5,350客户（91%）<br/>EIRE第二但仅5个客户"]
    GEO --> G3["启示：海外扩张有空间<br/>需区分零售 vs 批发客户"]

    ROOT --> PROD["📦 商品维度"]
    PROD --> P1["热销Top20 + 帕累托分析"]
    PROD --> P2["发现：长尾效应<br/>20%商品贡献78%收入<br/>接近二八法则"]
    PROD --> P3["异常：PAPER CRAFT<br/>1个买家 · 销量8万+ → 批发大客户"]
    PROD --> P4["启示：推荐系统聚焦头部<br/>POSTAGE/Manual不是真实商品"]

    ROOT --> USER["👥 用户维度"]
    USER --> U1["购买频次 · 消费额 · 商品种类数分布"]
    USER --> U2["发现：典型幂律分布<br/>中位数消费£899 vs 均值£3,019<br/>最高消费£608K"]
    USER --> U3["启示：不能用平均用户设计策略<br/>必须分层运营 → 引出RFM"]

    style ROOT fill:#f97316,stroke:#ea580c,color:#fff
```

### 分支三 — "怎么给用户分群？" → RFM分层

```mermaid
graph TD
    ROOT["🏷️ RFM模型<br/>基于业务规则的用户分层"]

    ROOT --> CALC["第一步：计算指标"]
    CALC --> R["R = Recency<br/>最后购买距基准日天数<br/>越小越好"]
    CALC --> F["F = Frequency<br/>购买次数（去重Invoice）<br/>越大越好"]
    CALC --> M["M = Monetary<br/>总消费金额<br/>越大越好"]

    ROOT --> SCORE["第二步：分位数打分"]
    SCORE --> SC1["各维度切5档（1-5分）<br/>R反转——天数小 = 分数高"]
    SCORE --> SC2["Frequency大量重复值<br/>→ rank(method='first')再qcut"]
    SCORE --> SC3["组合得到RFM字符串<br/>如'545' = R高F中高M高"]

    ROOT --> LABEL["第三步：标签映射"]
    LABEL --> SEG["8个用户群体"]
    SEG --> S1["💎 高价值忠诚<br/>1,300人 · 68.4%收入<br/>R≥4 F≥4 M≥4"]
    SEG --> S2["🌱 潜力客户<br/>975人 · 13.8%收入<br/>R≥3 F≥3 M≥3"]
    SEG --> S3["🚨 流失风险高价值<br/>227人 · 5.7%收入<br/>R≤2 F≥4 M≥4"]
    SEG --> S4["其他5群体<br/>3,376人 · 12.1%收入"]

    ROOT --> KEY["📋 核心发现：<br/>22%用户贡献68%收入<br/>用户价值高度集中"]

    style ROOT fill:#e74c3c,stroke:#dc2626,color:#fff
    style KEY fill:#fef3c7,stroke:#f39c12,color:#333
```

### 分支四 — "分群结果可信吗？" → K-Means交叉验证

```mermaid
graph TD
    ROOT["🤖 用数据驱动方法<br/>验证规则分层结果"]

    ROOT --> PREP["数据准备<br/>StandardScaler标准化R/F/M"]

    ROOT --> KSELECT["选K"]
    KSELECT --> ELBOW["肘部法则<br/>K=2到K=10 · 计算inertia"]
    KSELECT --> CHOOSE["K=4~5拐点最明显<br/>选K=5 · 与RFM群体数接近"]

    ROOT --> RESULT["聚类结果"]
    RESULT --> C0["优质活跃 383人<br/>R=28 F=28.5 M=£13,935"]
    RESULT --> C1["沉睡低价值 1,917人<br/>R=471 F=2.2 M=£756"]
    RESULT --> C2["超级大客户 24人<br/>R=22.5 F=119.8 M=£100,927"]
    RESULT --> C3["极端VIP 4人<br/>R=3.5 F=212.5 M=£436,836"]
    RESULT --> C4["一般活跃 3,550人<br/>R=75.7 F=5.1 M=£1,912"]

    ROOT --> CROSS["交叉验证<br/>crosstab + 热力图"]
    CROSS --> X1["✅ 一致：沉睡客户87%重叠<br/>两种方法高度吻合"]
    CROSS --> X2["🔍 新发现：4名极端VIP<br/>人均消费£43万 · 疑似批发<br/>RFM笼统归为'高价值忠诚'"]
    CROSS --> X3["🔍 新发现：24名超级大客户<br/>人均£10万 · RFM未区分层次"]

    ROOT --> CONCLUSION["📋 结论：<br/>RFM → 规则清晰，适合日常运营<br/>K-Means → 发现隐藏模式和极端值<br/>两者结合效果最佳"]

    style ROOT fill:#3498db,stroke:#2563eb,color:#fff
    style CONCLUSION fill:#fef3c7,stroke:#f39c12,color:#333
```

### 分支五 — "应该怎么做？" → 策略建议

```mermaid
graph TD
    ROOT["🎯 基于分层的差异化策略"]

    ROOT --> PRINCIPLE["核心原则<br/>80%资源投入P0-P2<br/>覆盖87.9%收入"]

    ROOT --> P0["🔴 P0 — 本周行动"]
    P0 --> P0A["流失风险高价值 227人<br/>根因：曾经高价值 · 平均341天未购<br/>措施：3封递进召回邮件<br/>+ 专属高额优惠券<br/>+ 电话回访了解流失原因<br/>指标：30天召回率 > 15%"]

    ROOT --> P1["🟡 P1 — 本月上线"]
    P1 --> P1A["高价值忠诚 1,300人<br/>根因：收入命脉 · 任何体验下降都致命<br/>措施：VIP权益体系<br/>+ 新品提前48h + 个性化推荐<br/>+ 积分计划增加迁移成本<br/>指标：季度留存率 > 95%"]
    P1 --> P1B["潜力客户 975人<br/>根因：最大增量来源<br/>措施：累计满£5K解锁VIP<br/>+ 高价值未购品类推荐<br/>指标：高价值转化率 > 8%"]

    ROOT --> P2["🟢 P2 — 持续运营"]
    P2 --> P2A["新客户 443人<br/>→ 7天引导 + 14天二单9折"]
    P2 --> P2B["沉睡客户 1,523人<br/>→ 低成本触达 · 分批20%测试"]

    ROOT --> AB["🧪 A/B测试"]
    AB --> AB1["召回邮件序列<br/>对照：无干预 · 实验：3封递进邮件<br/>观测：30天复购率 · 周期：4周"]
    AB --> AB2["阶梯激励<br/>对照：常规促销 · 实验：消费里程碑<br/>观测：60天消费额 · 周期：8周"]
    AB --> AB3["二单转化<br/>对照：无干预 · 实验：14天内9折<br/>观测：30天二购率 · 周期：4周"]

    style ROOT fill:#2ecc71,stroke:#16a34a,color:#fff
    style P0 fill:#fee2e2,stroke:#e74c3c,color:#333
    style P1 fill:#fef3c7,stroke:#f39c12,color:#333
    style P2 fill:#dcfce7,stroke:#2ecc71,color:#333
```



---

## 📈 关键发现

| 维度 | 发现 | 产品启示 |
|------|------|---------|
| ⏰ 时间 | 每年 9-11 月销售额攀升 40%+，11 月达峰值 | Q3 开始备战圣诞营销 |
| 🌍 地域 | 英国贡献 83% 收入，海外市场零散 | 海外扩张需区分零售与批发 |
| 📦 商品 | 20% 商品贡献 78% 收入 | 推荐系统聚焦头部商品 |
| 👥 用户 | 幂律分布，中位数消费仅 £899 | 不能用平均用户设计策略 |

---

## 🏷️ 用户分层结果

通过 RFM 模型将 5,878 名客户分为 8 个群体：

| 群体 | 人数 | 收入占比 | 核心策略 |
|------|------|---------|---------|
| 💎 高价值忠诚客户 | 1,300 | 68.4% | VIP权益 · 个性化推荐 · 积分计划 |
| 🌱 潜力客户 | 975 | 13.8% | 阶梯激励 · 品类拓展 · 限时优惠 |
| 🚨 流失风险高价值 | 227 | 5.7% | 紧急召回 · 专属优惠 · 原因回访 |
| 👤 一般客户 | 1,102 | 4.6% | 常规维护 |
| 💤 沉睡客户 | 1,523 | 3.8% | 低成本触达 · 分批测试 |
| 👋 新客户 | 443 | 2.2% | 新手引导 · 二单激励 |
| 🔄 高频低消客户 | 182 | 0.9% | 交叉销售 · 提升客单价 |
| ⚠️ 流失风险一般价值 | 126 | 0.6% | 观望 · 低优先级 |

---

## 🤖 K-Means 聚类验证

使用 K-Means（K=5）对 RFM 数据进行无监督聚类，与手动分层交叉验证：

- ✅ **一致**：沉睡客户的 87% 被两种方法同时识别
- 🔍 **新发现**：K-Means 识别出 4 名"极端 VIP"（人均消费 £43 万，疑似批发客户）和 24 名"超级大客户"（人均 £10 万），RFM 未能区分这些极端群体
- 💡 **结论**：RFM 适合日常运营（规则清晰），K-Means 补充发现隐藏模式，两者结合效果最佳

---

## 💡 策略优先级

```
P0 🚨 流失风险高价值  →  本周启动召回（227人 · 已验证的高LTV用户）
P1 💎 高价值忠诚客户  →  本月上线VIP体系（1,300人 · 收入命脉）
P2 🌱 潜力客户       →  本月启动阶梯激励（975人 · 最大增量来源）
P3 👋 新客户         →  持续运营二单转化（443人 · 培育长期价值）
P4 💤 沉睡客户       →  分批测试低成本触达（1,523人 · 不过度投入）

核心原则：80% 资源投入 P0-P2，覆盖 87.9% 收入
```

---

## 🛠️ 技术栈

| 模块 | 工具 |
|------|------|
| 数据处理 | Python · pandas · numpy |
| 可视化 | matplotlib · seaborn · plotly |
| 机器学习 | scikit-learn（KMeans · StandardScaler · PCA） |
| Dashboard | Streamlit |

---

## 📁 项目结构

```
ecommerce-user-analysis/
├── notebooks/
│   ├── 01_data_cleaning.ipynb    # 数据清洗
│   ├── 02_eda.ipynb              # 探索性分析
│   ├── 03_rfm_analysis.ipynb     # RFM 用户分层
│   ├── 04_clustering.ipynb       # K-Means 聚类验证
│   └── 05_insights.ipynb         # 产品策略建议
├── dashboard/
│   └── app.py                    # Streamlit Dashboard
├── data/                         # 数据目录（未上传）
└── docs/                         # 文档与截图
```

---

## 🚀 快速开始

```bash
# 克隆项目
git clone https://github.com/wodaima/ecommerce-user-analysis.git
cd ecommerce-user-analysis

# 创建虚拟环境
python -m venv .venv
.venv\Scripts\activate        # Windows
# source .venv/bin/activate   # Mac/Linux

# 安装依赖
pip install pandas numpy matplotlib seaborn plotly scikit-learn streamlit openpyxl jupyter

# 下载数据集
# 从 https://archive.ics.uci.edu/dataset/502/online+retail+ii 下载
# 将 online_retail_II.xlsx 放入 data/ 目录

# 运行分析（按顺序执行 Notebook）
jupyter notebook

# 启动 Dashboard
cd dashboard
streamlit run app.py
```

---

## 📊 Dashboard 预览

> 截图/GIF 待补充

---

## 📝 License

MIT
