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
- Slightly worse average error (MAE) than persistence, but improves RMSE by handling spikes better.  

---

### 3. SARIMA (Seasonal ARIMA)
- Parameters: `(2,1,2)(1,1,1,27)`  
  - Short-term memory (last 2 days, last 2 forecast errors)  
  - Seasonal cycle (27 days, ~solar rotation)  
- **MAE ≈ 14.9**  
- **RMSE ≈ 24.7**  
- Fit converged, captured the repeating 27-day cycle, but forecasts were overly smoothed. Underperformed persistence overall.  

---

### 4. Random Forest (Walk-Forward Validation)
- Inputs: last 27 days of flux as lag features  
- Output: flux 7 days ahead  
- Evaluated using rolling walk-forward splits to mimic real forecasting  
- **MAE ≈ _(your measured value)_**  
- **RMSE ≈ _(your measured value)_**  
- Handles nonlinear spikes better than linear/SARIMA models, though still challenged by extreme events.  

---

## Current Takeaways
- **Persistence** remains a tough baseline to beat during quiet solar activity.  
- **Linear Regression** reduces RMSE slightly by catching some spikes.  
- **SARIMA** models periodic cycles well but oversmooths and lags behind spikes.  
- **Random Forest** introduces nonlinearity, showing promise in handling more complex dynamics.  
- Traditional linear/statistical models still **struggle with nonlinear, spiky solar flux patterns**.  

---

## Next Steps
- Tune **Random Forest hyperparameters** (trees, depth, features) to reduce variance.  
- Try **Gradient Boosting (XGBoost/LightGBM)** for stronger nonlinear modeling.  
- Experiment with **Neural Networks (RNNs/LSTMs)** to directly model sequential dynamics.  
- Expand walk-forward validation and visualizations to compare models under both quiet and active solar periods.  

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

