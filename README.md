# AI-Powered Business Intelligence & Predictive Analytics Platform

An interactive retail analytics project built with **Python, Streamlit, Plotly, XGBoost, and scikit-learn**. It explores historical retail transactions, summarizes business performance, forecasts daily sales, groups customers using RFM features, and flags unusual sales days.

## Features

- **Business KPIs:** total revenue, orders, unique customers, unique products, and average order value.
- **Revenue analysis:** monthly revenue trends, top products, and country-level performance.
- **Sales forecasting:** an XGBoost regression model with lag, rolling-statistic, and calendar features. The dashboard supports a configurable forecast horizon.
- **Forecast evaluation:** chronological 80/20 holdout evaluation using MAE, RMSE, R², and WAPE.
- **Customer segmentation:** Recency, Frequency, and Monetary (RFM) features transformed with `log1p`, standardized, and clustered with K-Means.
- **Anomaly detection:** Isolation Forest identifies statistically unusual daily sales observations.
- **Downloads:** export cleaned transactions, forecasts, anomaly reports, forecast metrics, and customer-level segment data as CSV files.

## Repository structure

```text
.
├── app.py
├── requirements.txt
├── README.md
├── .gitignore
├── notebooks/
│   ├── business_intelligence_analysis.ipynb
│   └── business_intelligence_colab_export.py
└── models/
    └── anomaly_detection_model.pkl
```

The Streamlit app trains its forecasting, segmentation, and anomaly-detection models from the selected dataset when the app runs. The included pickle file is a separately supplied anomaly-detection artifact; the current Streamlit app does not load it.

## Dataset

The project expects retail transaction data with these columns:

| Column | Description |
|---|---|
| `InvoiceNo` | Invoice / order identifier |
| `StockCode` | Product identifier |
| `Description` | Product description |
| `Quantity` | Quantity purchased |
| `InvoiceDate` | Transaction timestamp |
| `UnitPrice` | Unit price |
| `CustomerID` | Customer identifier |
| `Country` | Customer country |

The notebook downloads the **UCI Online Retail** Excel dataset automatically when possible and offers a manual upload fallback.

Dataset source: [UCI Machine Learning Repository – Online Retail](https://archive.ics.uci.edu/dataset/352/online+retail)

For the Streamlit app's built-in sample-data option, place the downloaded file in the repository root and name it `Online Retail.xlsx`. Alternatively, upload a `.csv`, `.xlsx`, or `.xls` file in the app sidebar.

> The app validates the required columns and removes rows with missing core transaction fields, cancelled invoices (invoice numbers beginning with `C`), non-positive quantities, and non-positive unit prices before calculating revenue.

## Setup

Python 3.10 or newer is recommended.

### 1. Clone the repository

```bash
git clone https://github.com/<your-username>/<your-repository>.git
cd <your-repository>
```

### 2. Create and activate a virtual environment

**macOS / Linux**

```bash
python -m venv .venv
source .venv/bin/activate
```

**Windows PowerShell**

```powershell
python -m venv .venv
.venv\Scripts\Activate.ps1
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. Run the dashboard

```bash
streamlit run app.py
```

Open the local URL printed by Streamlit (normally `http://localhost:8501`).

## Using the dashboard

1. Upload a retail dataset from the sidebar, or enable the sample dataset option after placing `Online Retail.xlsx` in the repository root.
2. Set the forecast horizon (7–60 days), number of customer clusters (2–8), and display currency symbol.
3. Select the date range to analyse.
4. Explore the Business Overview, Sales Forecast, Customer Segments, Anomaly Detection, and Data & Downloads tabs.
5. Download the CSV outputs from the final tab or the forecast / anomaly sections.

The dashboard requires at least **45 days** of daily observations to create forecasting features and train/evaluate the sales model.

## Run the notebook

Open `notebooks/business_intelligence_analysis.ipynb` in Google Colab or Jupyter. The notebook includes package installation, data acquisition, cleaning, exploratory analysis, forecasting, RFM segmentation, anomaly detection, feature importance, SHAP interpretation, and export of generated models and reports.

The companion `business_intelligence_colab_export.py` is a Colab-oriented script export. It contains notebook-style shell commands and Google Colab upload/download helpers, so the notebook is the recommended way to run that workflow.

## Methodology notes

- Daily sales are calculated by summing transaction revenue by calendar day; missing dates are filled with zero.
- Forecast features include sales lags (1, 2, 3, 7, 14, and 28 days), rolling means and standard deviations (7, 14, and 28 days), and calendar features.
- The time series is split chronologically: the first 80% of feature rows train the model and the final 20% form the holdout set.
- Forecasts are recursively generated, feeding each predicted day into the history used for the next day.
- Customer RFM values are calculated per `CustomerID`; K-Means cluster labels are numeric identifiers and do not imply a predefined customer ranking.
- Isolation Forest flags statistical outliers. An anomaly is not, by itself, proof of fraud, an error, or a business problem.

## Important limitations

- Forecasts are estimates based on historical patterns and are not guaranteed outcomes.
- The model is evaluated on a chronological holdout, but the notebook and app do not establish that the model will generalize to every retailer or future period.
- Cluster IDs are arbitrary; interpret segments using the accompanying RFM summary rather than the numeric label alone.
- The sample dataset is a historical dataset and should not be treated as live business data.
- The dashboard retrains models during analysis; runtime depends on dataset size and available hardware.

## Tech stack

- Python
- Streamlit
- Pandas and NumPy
- Plotly
- scikit-learn (StandardScaler, K-Means, Isolation Forest, evaluation metrics)
- XGBoost
- Jupyter / Google Colab
- SHAP (notebook explainability workflow)

## Model artifact note

`models/anomaly_detection_model.pkl` is a supplied serialized scikit-learn model artifact. Pickle/joblib files can execute code during deserialization. Only load model files from sources you trust, and use a compatible Python / scikit-learn environment. The Streamlit dashboard currently trains its own anomaly detector and does not require this file.

## Future improvements

- Add automated tests and a reproducible model-training pipeline.
- Persist and version all trained models with metadata.
- Add configurable model selection and backtesting across multiple forecast windows.
- Add richer customer-segment descriptions and business recommendations.
- Add deployment configuration for Streamlit Community Cloud or another hosting provider.

## Acknowledgements

- Dataset: [UCI Online Retail](https://archive.ics.uci.edu/dataset/352/online+retail)
- Open-source Python data science and machine-learning ecosystem.
