# 🚦 Clustering Indian States, UTs & Cities by Traffic-Accident Severity (K-Means)

An unsupervised machine-learning project that groups Indian States, Union Territories and major metropolitan cities into **accident-severity risk tiers (High / Medium / Low)** using K-Means clustering on official government traffic-accident statistics.

## 📌 Project Objectives

- Clean the official ADSI traffic-accident table (`ADSI_Table_1A.2_2.csv`) so that only individual geographical entities are analysed.
- Scale features so Euclidean distances are meaningful across very different metric magnitudes.
- Find the number of clusters with the **Elbow Method** and cross-check with the **Silhouette Score**.
- Fit **K-Means**, label clusters as risk tiers, evaluate them and visualise the result.

## 🗂️ Dataset

| Item                | Detail                                                                                            |
| File                | `ADSI_Table_1A.2_2.csv` (encoding `latin1`)                                                       |
| Source              | Accidental Deaths & Suicides in India (ADSI), Table 1A.2 – Traffic Accidents                      |
| Raw rows            | 93 (incl. 4 summary rows: `Total (States)`, `Total (UTs)`, `Total (All India)`, `Total (Cities)`) |
| Rows after cleaning | **88** (28 States + 8 UTs + 52 Cities)                                                            |
| Features used       | Cases, Injured, Died × {Road, Railway, Railway Crossing} = **9 features**                         |

## 🏗️ Architecture / Pipeline

```
CSV ──► Load (latin1) ──► Drop "Total"/"All India" rows ──► Clean names, nulls, duplicates
    ──► Select 9 features ──► StandardScaler ──► Elbow (K=1..10) + Silhouette
    ──► KMeans(K=3, k-means++) ──► Rank clusters by mean deaths → High / Medium / Low
    ──► Silhouette + Davies-Bouldin ──► Scatter plot with centroids + cluster profiles
```

### Key design decisions

- **Summary rows removed:** they are sums of many entities, so they would become extreme outliers, drag centroids and double-count accidents.
- **`StandardScaler`:** K-Means uses Euclidean distance; without scaling, large-valued columns (road cases) swamp small ones (crossing deaths).
- **Totals excluded from training:** `Total Traffic Accidents` = road + railway + crossing, so it is redundant; it is only used for plotting.
- **Duplicate handling:** `Chandigarh (city)` is numerically identical to the `Chandigarh` UT and is dropped.

## 👥 Team Role Breakdown

| Role                   | Responsibilities                                                                                        |
| ---------------------- | ------------------------------------------------------------------------------------------------------- |
| **Data Preprocessing** | Load CSV, remove summary rows, clean names, handle nulls/duplicates, feature selection, scaling         |
| **ML Modeling**        | Elbow method, K selection, K-Means fitting, risk-tier labelling, Silhouette / Davies-Bouldin evaluation |
| **Documentation**      | Markdown explanations, visualisations, README, presentation summary                                     |

## ▶️ How to Run in Google Colab

1. Open [Google Colab](https://colab.research.google.com) → **File → Upload notebook** → select `Accident_Risk_KMeans.ipynb`.
2. Click **Runtime → Run all**.
3. When prompted in Step 1, click **Choose files** and upload `ADSI_Table_1A.2_2.csv`
   (or upload it beforehand via the 📁 Files panel on the left).
4. Scroll through the outputs: elbow plot, Silhouette score, scatter plot, tier members.

> Runs locally too: `pip install pandas numpy matplotlib seaborn scikit-learn jupyter`.

## 📊 Key Results & Insights

| Metric                 | Value                          |
| ---------------------- | ------------------------------ |
| Chosen K               | **3** (High / Medium / Low)    |
| Silhouette Score (K=3) | **≈ 0.76** (strong separation) |
| Highest Silhouette     | K = 2 (≈ 0.81)                 |

| Risk tier               | Members                                                                                                          | Avg. total deaths |
| ----------------------- | ---------------------------------------------------------------------------------------------------------------- | ----------------- |
| 🔴 **High Risk** (2)    | Uttar Pradesh, West Bengal                                                                                       | ≈ 18,400          |
| 🟠 **Medium Risk** (10) | Tamil Nadu, Maharashtra, Madhya Pradesh, Rajasthan, Karnataka, Andhra Pradesh, Bihar, Gujarat, Telangana, Kerala | ≈ 11,900          |
| 🟢 **Low Risk** (76)    | All other States, all UTs and all 52 cities                                                                      | ≈ 740             |

**Insights**

- The data is **extremely skewed**: a dozen large states account for most accidents, so the Low-Risk tier is very large.
- **Uttar Pradesh and West Bengal form their own tier** mainly because of unusually high **railway and railway-crossing** accidents, not just road accidents.
- **Every city falls in the Low tier** because city-level counts are far smaller than state-level counts – cities and states should ideally be clustered separately.
- Silhouette is higher at K=2, but K=3 is kept to match the High/Medium/Low framework and still scores strongly.

## ⚠️ Limitations

- Absolute counts, not rates – large populations/road networks naturally record more accidents (use per-capita or per-km features next).
- Cities overlap with their parent State/UT.
- K-Means is outlier-sensitive; a `log1p` transform, DBSCAN or hierarchical clustering are good extensions.

## 🧰 Tech Stack

Python · pandas · NumPy · scikit-learn · matplotlib · seaborn · Google Colab

## 📁 Repository Structure

```
├── Accident_Risk_KMeans.ipynb
├── ADSI_Table_1A.2_2.csv
└── README.md
```
