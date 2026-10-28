# Customer Lifetime Value (CLV) Prediction Using Data Analytics

MCA (Data Analytics), Galgotias University
Abhishek Joshi (25132160069) | Shivam Upmanyu (25132160063) | Harshit Gaikwad (25132160036)

## How to run (3 steps)
1. Install Python 3.10+ and run: `pip install -r requirements.txt`
2. Run: `jupyter notebook CLV_Prediction_Project.ipynb`
3. Click **Kernel > Restart & Run All**. It takes about 30 seconds.

The dataset `Online_Retail.csv` must stay in the same folder as the notebook. (`Online Retail.xlsx` also works.)
Results are saved in the `clv_outputs/` folder. No internet is needed.
To only look at the results, open `CLV_Prediction_Project.html` in any browser.

## What is inside
| File | Purpose |
|---|---|
| `CLV_Prediction_Project.ipynb` | The full project: cleaning, analysis, models, segments, recommendations |
| `CLV_Prediction_Project.html` | Same notebook with results, readable without Python |
| `clv_outputs/customer_clv_segments.csv` | One row per customer: RFM, predicted value, segment. **Load this in Power BI** |
| `clv_outputs/segment_summary.csv` | Customers and revenue per segment |
| `clv_outputs/model_comparison.csv` | Accuracy of all models |
| `clv_outputs/monthly_revenue.csv` | Monthly revenue and customers |
| `clv_outputs/retention_by_recency.csv` | Evidence for the churn rule |
| `clv_outputs/fig1...fig9.png` | Charts for slides and the report |

## Results (from the run in this folder)
- **Data:** 541,909 raw rows cleaned to 391,150 rows, 4,334 customers, GBP 8.74 million revenue (Dec 2010 to Dec 2011).
- **Revenue is concentrated:** the top 20% of customers produce 75% of revenue; the top 1% produce 32%.
- **Test design:** models learn from data up to 9 Sep 2011 and are scored on real spend in the next 91 days (5-fold cross-validation).

| Model | Typical error (MAE) | Top-20% revenue captured |
|---|---|---|
| Baseline: same average for all | GBP 1,037 | 18% |
| Baseline: past spend rate | GBP 888 | 62% |
| Linear Regression | GBP 666 | 62% |
| Random Forest | GBP 732 | 62% |
| Gradient Boosting | GBP 717 | 62% |
| **BG/NBD + Gamma-Gamma** | **GBP 626** | **65%** (perfect foresight = 82%) |

BG/NBD + Gamma-Gamma has the lowest error and the best ranking, but Linear Regression has the best RMSE and R2. The differences are modest and a simple "keep spending as before" rule is competitive. Do not claim a big win.

| Segment | Customers | Share of predicted 12-month revenue |
|---|---|---|
| High-Value | 839 (19%) | 62% |
| At-Risk High-Value | 63 (1.5%) | 2% |
| Medium-Value | 1,277 (29%) | 21% |
| Low-Value | 2,155 (50%) | 15% |

## Things to know before you present
- **BG/NBD "probability alive" did not work on this data** (about 1.00 for everyone). Churn risk is therefore measured from recency, and the notebook shows the evidence: top spenders silent for more than 90 days came back only 56% of the time, against 90% for active ones.
- **BG/NBD under-predicts purchases by about 25%** because the test period (Sep-Dec) is the Christmas peak, 1.7 times busier than the earlier months. The model assumes a constant buying rate.
- **The `lifetimes` package is not used.** It fails to converge on current NumPy versions, so BG/NBD and Gamma-Gamma are coded directly from the original papers. The likelihood was checked and matches the package's own formula exactly.
- The 12-month CLV is an extrapolation and cannot be checked against future data.

## Power BI dashboard (suggested build, about 1 hour)
1. **Get Data > Text/CSV**: load `customer_clv_segments.csv`, `segment_summary.csv`, `monthly_revenue.csv`.
2. **Page 1 - Overview:** cards for customer count, total past revenue (`SUM(Monetary_total_GBP)`) and predicted 12-month revenue (`SUM(CLV_12m_GBP)`); donut chart of customers by `Segment`; bar chart of `CLV_12m_GBP` by `Segment`; slicers for `Country` and `Segment`.
3. **Page 2 - Customers:** scatter plot (X = `Recency_days`, Y = `Monetary_total_GBP`, colour = `Segment`); table of the top 20 customers by `CLV_12m_GBP`.
4. **Page 3 - Trend:** line chart of `Revenue` by `Month` from `monthly_revenue.csv`.
5. Segment colours used in the notebook: High-Value teal `#2A9D8F`, At-Risk red `#D1495B`, Medium amber `#E9C46A`, Low grey `#B0B7C3`.

## Dataset
UCI Machine Learning Repository, "Online Retail" (Chen, Sain & Guo, 2012): UK online gift retailer, 1 Dec 2010 to 9 Dec 2011.
