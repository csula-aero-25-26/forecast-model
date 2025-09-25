# CSULA Aerospace Senior Design – F10.7 Solar Flux Forecasting

## Project Overview
This project is part of the **CSULA Aerospace Senior Design Team (Fall 2025)**.  
Our goal is to forecast the **F10.7 solar radio flux index**, a key measure of solar activity widely used in space weather modeling, atmospheric drag calculations, and satellite operations.

The F10.7 index is measured daily in solar flux units (sfu) and reflects solar emissions at a wavelength of 10.7 cm.  
Accurate forecasting is important for predicting satellite drag, GPS accuracy, and communication reliability during periods of solar activity.

---

## Dataset
- **Source:** [NOAA SWPC – Penticton F10.7 dataset](https://www.swpc.noaa.gov/phenomena/f107-cm-radio-emissions)  
- **Range:** 1947–present  
- **Preprocessing steps:**
  - Converted raw text file to a Pandas DataFrame  
  - Averaged multiple daily measurements into one daily adjusted flux value  
  - Created a `target_flux` column = flux 7 days into the future  
  - Built lag features (`lag1` … `lag27`) to give models short-term memory  

---

## Models Tested

### 1. Persistence Baseline
- Forecast: “Flux 7 days ahead = today’s flux”  
- **MAE ≈ 12.8**  
- **RMSE ≈ 23.4**  
- Strong simple benchmark, works during quiet periods but fails at solar spikes.  

---

### 2. Linear Regression with Lag Features
- Inputs: last 27 days of flux (`lag1` … `lag27`)  
- Output: flux 7 days ahead (`target_flux`)  
- **MAE ≈ 13.8**  
- **RMSE ≈ 22.7**  
- Did not beat persistence overall: higher average error (MAE), though RMSE was slightly lower by smoothing spikes.  

---

### 3. SARIMA (Seasonal ARIMA)
- Parameters: `(2,1,2)(1,1,1,27)`  
  - Short-term memory (autoregressive + moving average terms)  
  - Seasonal cycle (27 days, ~solar rotation)  
- **MAE ≈ 14.9**  
- **RMSE ≈ 24.7**  
- Fit converged, captured the repeating 27-day cycle, but forecasts were overly smoothed. Performed worse than persistence overall.  

---

### 4. Random Forest (Walk-Forward Validation, 100 Trees)
- Inputs: last 27 days of flux as lag features  
- Output: flux 7 days ahead  
- Evaluated using walk-forward validation to simulate real forecasting  
- **MAE ≈ 16.9**  
- **RMSE ≈ 23.9**  
- Did not beat persistence. Predictions were smoother and reduced variance, but errors on spikes increased.  

---

### 5. Random Forest (Alternative Lag Design: 7 recent days + 27–33 days ago)
- Inputs: last 7 days + same 7-day window one solar rotation ago  
- Output: flux 7 days ahead  
- Evaluated with walk-forward validation  
- **MAE ≈ 21.2**  
- **RMSE ≈ 29.6**  
- Performed worse than both persistence and the 27-lag Random Forest. Limiting to only 14 lags reduced context and degraded accuracy.  

---

## Current Takeaways
- **Persistence** is a very strong baseline — difficult to beat with simple models.  
- **Linear Regression** and **SARIMA** underperformed persistence, confirming that linear/statistical approaches struggle with nonlinear solar activity.  
- **Random Forest** on lagged features did not beat persistence (higher MAE, similar RMSE).  
- **Alternative lag design** (recent + rotational) performed worse, suggesting that reducing lags loses valuable context.  
- So far, traditional models fail to consistently improve on persistence.  

---

## Next Steps
- Run a **500-tree Random Forest** overnight to check for stability, but large improvements are unlikely.  
- Try **Gradient Boosted Trees (XGBoost/LightGBM)**, which often outperform Random Forest on tabular data.  
- Engineer domain features:
  - Rolling averages (7-day, 27-day)  
  - Sunspot numbers (high correlation with F10.7)  
  - Cycle indicators (27-day rotation, ~11-year cycle phase)  
- Explore **Neural Networks (LSTM/GRU/TCN)** for sequence modeling once simpler models are exhausted.  
- Continue using **walk-forward validation** for realistic performance estimates during both quiet and active solar periods.  

---

## How to Run
### 1. Clone this repo:  
   ```bash
   git clone https://github.com/csula-aero-25-26/forecast-model.git
   cd forecast-model
   ```
### 2. Create a Virtual Environment
It is recommended to keep dependencies isolated in a virtual environment.

```bash
python -m venv .venv
```

On Linux/MAC: 
```bash
source .venv/bin/activate
```
On Windows: 
```bash 
.venv\Scripts\activate
```

### 3. Install Dependencies
After activating the environment, install required packages:
```bash
pip install -r f107-baseline/requirements.txt
```

# Launch Jupyter Notebook

Once your environment is set up and dependencies are installed:

1. Start Jupyter:
```bash
jupyter notebook
```

# Team
This project is part of the **CSULA Aerospace Senior Design Team (Fall 2025 - Spring 2026)**.

**Advisor:**  
- Dr. Zilong Ye (zye5@calstatela.edu)

**Team Members:**  
- Joshua Hanscom  
- [Add teammates here]  

---

## Acknowledgments
- Data: [NOAA SWPC – F10.7 cm Radio Emissions](https://www.swpc.noaa.gov/phenomena/f107-cm-radio-emissions)  
- Tools: Python, Pandas, NumPy, Matplotlib, scikit-learn, Statsmodels, Jupyter

