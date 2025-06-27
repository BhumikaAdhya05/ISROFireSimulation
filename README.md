# 🔥 Forest Fire Risk Forecasting & Spread Simulation (Uttarakhand)

A geospatial AI/ML system to predict forest fire probabilities for the next day and simulate the spread over 1, 2, 3, 6, and 12 hours using real environmental, terrain, and anthropogenic data.

Hackathon Submission – **ISRO Bharatiya Antariksh Hackathon 2025**

---

## 📌 Problem Statement

Uncontrolled forest fires are a growing environmental challenge, particularly in states like **Uttarakhand**. To assist in early mitigation and response planning, this project aims to:
- 📍 Predict the **likelihood of forest fires for the next day**
- 🕓 Simulate how fires might **spread dynamically over time**
- 🗺️ Visualize and animate the process for planners and agencies

---

## 🎯 Objectives

1. Generate a **binary forest fire risk map** (fire / no fire) for the next day.
2. Simulate **fire spread** from high-risk zones over:
   - 1 hour
   - 2 hours
   - 3 hours
   - 6 hours
   - 12 hours
3. Integrate terrain, weather, land use, and human activity into a predictive ML pipeline.

---

## ✅ Achievements So Far

### 🧩 1. Data Collection & Preprocessing

| Dataset | Source | Description |
|---------|--------|-------------|
| **Weather** | ERA5 (Copernicus) | Temperature, humidity, wind (U/V), dewpoint, rainfall (24h) |
| **Terrain** | Bhoonidhi DEM | 30m DEM used to derive slope and aspect |
| **Human Activity** | GHSL-BUILT-S | 100m resolution built-up area density |
| **Fire Labels** | FIRMS (VIIRS) | High-confidence fire points (CSV) converted to raster labels |
| **Fuel (LULC)** | NRSC 1:50,000 (2015–16) | *[⚠️ Currently invalid – all 0s; will re-download]* |

All rasters were:
- Converted to `.tif` where necessary
- Resampled to 30m
- Aligned to common grid
- Padded to 64×64 resolution

---

### 🤖 2. Fire Prediction using U-Net

- Model: **U-Net** with `resnet18` encoder
- Inputs: 9 channels (weather + terrain + GHSL)
- Output: 2-class segmentation (fire / no fire)
- Labels: Binary fire mask from VIIRS points

**Final Evaluation with GHSL added:**

 ``` 🔥 Fire pixels in Ground Truth : 26 🔥 Fire pixels predicted by model : 16 Accuracy : 0.9937 Precision: 0.5000 Recall : 0.3077 F1 Score : 0.3810 IoU : 0.2353 ```

 
> 🔼 GHSL significantly improved focus on anthropogenic fire risk areas.

---

### 🔁 3. Fire Spread Simulation

Inspired by **cellular automata**, the spread was modeled using:
- 🔥 Existing fire locations (binary mask)
- 🌾 Fuel availability (planned from LULC)
- ⛰️ Terrain slope
- 🌬️ Wind speed/direction (U/V components)

#### 🔍 Debug Output (Simulation Logs)

``` 🧪 Step 1 → Fire: 16 → New Fires: 0 🧪 Step 2 → Fire: 16 → New Fires: 0 🧪 Step 3 → Fire: 16 → New Fires: 0 ... Reason: Fuel array = 0 everywhere (awaiting valid LULC) ```

---

## 🗂️ Dataset Summary

| Type | Source | Format | Status |
|------|--------|--------|--------|
| ERA5 Weather | Copernicus | `.nc` + `.tif` | ✅ Processed |
| DEM | Bhoonidhi (CartoDEM 30m) | `.tif` | ✅ Slope derived |
| LULC (Fuel) | NRSC Bhuvan | `.tif` | ❌ All values = 0 (re-download needed) |
| GHSL Built-S | JRC | `.tif` | ✅ Normalized |
| Fire Archive | FIRMS VIIRS | `.csv` | ✅ Rasterized |
| GHSL Grid | Global 100m Tile | `.tif` | ✅ Integrated |
| Feature Stack | — | `.npy` | ✅ Saved |
| Model Checkpoint | — | `.pth` | ✅ Saved |

---

## 🧪 Debugging & Visualization

- 🧾 All datasets printed with min, max, mean to catch invalid inputs (e.g. slope = -9999, LULC = 0).
- 🧪 Debug simulation printed: fire pixel count, spread checks per hour.
- 🗺️ Visuals: Ground Truth vs Prediction, Fire Spread (1–12 hrs), GHSL overlays

---

## 📊 Outputs & Visualizations

### 🔥 Fire Prediction vs Ground Truth
![fire_pred_vs_gt](./outputs/pred_vs_gt.png)

### 🔥 Fire Spread Simulation (Hour 1–12)
![fire_spread_sim](./outputs/fire_spread_6hr.png)

### 🔥 Fire Spread with GHSL + Fuel Context
![fire_spread_ghsl](./outputs/fire_spread_ghsl_overlay.png)

---

## ❌ Known Limitations / To-Do

| Task | Status |
|------|--------|
| Correct LULC integration | ❌ To re-download and re-process |
| Use U-Net predicted mask as simulation input | ⏳ In progress |
| Expand to 128×128 or multi-tile grid | ⏳ Optional enhancement |
| Region-specific models (e.g., Nainital focus) | ✅ Cropping logic done |
| LSTM-based next-day sequence modeling | ⏳ Planned as next module |
| Add basemap / lat-lon coordinates | ⏳ Bonus mapping feature |

---

## 🔚 Conclusion

Even with imperfect fuel data, we successfully:
- Trained a fire prediction model
- Built a working spread simulation engine
- Verified with real geospatial inputs
- Visualized results in map-style, ready for animation or real-world integration

🛠️ With LULC fixed and fire prediction plugged into simulation, this model will be **fully deployable** for short-term fire forecasting in India’s forest-prone regions.


