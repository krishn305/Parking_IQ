# ParkingIQ — Bengaluru Parking Intelligence Platform

> AI-driven system that turns ~298,000 parking-violation records into targeted, predictive enforcement for Bengaluru Traffic Police.

**Key finding:** Just 24 hotspot zones — under 1% of 2,534 total H3 grid cells — account for ~17% of all violations.

---

## The Problem

Bengaluru's roads lose significant carriageway capacity every day to illegal parking. Enforcement is largely reactive: officers patrol fixed beats rather than responding to where and when violations are most likely to occur. Heavy vehicles blocking junctions, habitual offenders never escalated beyond a base fine, and under-resourced police stations applying uniform patrol schedules all compound the problem.

ParkingIQ converts six months of enforcement records into spatial intelligence — identifying where violations cluster, predicting when they will peak, and prescribing exactly where to deploy officers and infrastructure.

---

## How It Works

The pipeline follows five analytical stages:

| Stage | What it does |
|-------|-------------|
| **Detect** | H3 (resolution 9, ~100 m hexagons) bins every violation into a spatial grid cell |
| **Quantify** | Violation density, repeat-offender share, heavy-vehicle share, junction proximity, and temporal spread are computed per cell |
| **Characterise** | Each cell is labelled CHRONIC, SEMI-CHRONIC, or SPORADIC based on persistence and variance |
| **Predict** | A Random Forest Regressor trained on spatial and temporal features forecasts hourly violation volume per police station |
| **Prescribe** | Patrol windows, officer deployment counts, petrol-pump placement, and escalating fine tiers are recommended from the forecasts |

### Zone Risk Index (ZRI)

Composite score from 0–10 that ranks every hotspot cell:

| Component | Weight |
|-----------|--------|
| Violation density (log-normalised) | 35% |
| Heavy-vehicle ratio | 20% |
| Repeat-offender ratio | 15% |
| Spillover index (spatial spread) | 15% |
| Junction proximity | 15% |

---

## Features

### Enforcement Intelligence
| Page | Description |
|------|-------------|
| **Overview** | Executive dashboard — KPIs, top-10 hotspot table, violation-type breakdown |
| **Hotspot Map** | Interactive Folium map (grid-marker or heatmap) with per-zone popups; colour-coded by ZRI |
| **Zone Risk Analysis** | Spillover vs. persistence scatter, zone-type composition, police-station comparison, full registry table |
| **Peak Hour Forecast** | RandomForestRegressor forecasts hourly violation volume; patrol-window recommender; 24×7 heatmap; officer deployment simulator |
| **Petrol Pump Simulator** | Models the effect of adding designated refuelling lay-bys on short-stop violations and corridor ZRI |
| **Habitual Offender Engine** | 30-day watchlist, vehicle-plate lookup, escalating fine tiers (₹500 → ₹5,000), batch summons export |
| **Live Feed Replay** | Historical records replayed as a simulated real-time ticker with watchlist intercept alerts |

### Civic Features
| Page | Description |
|------|-------------|
| **Citizen Complaints** | Public grievance form — zone, violation type, photo evidence; aggregated with enforcement data |
| **SMS Alert Center** | Simulated SMS gateway — dispatch penalty warnings to individual or bulk-tier offenders (demo only) |
| **Zone Improvement Tracker** | Log bollards, signage, patrols, towing drives; models compounded ZRI reduction before vs. after |
| **Safe Parking Finder** | Haversine-radius search for low-ZRI zones near a destination; map with safe/avoid colour coding |

---

## Tech Stack

| Layer | Libraries |
|-------|-----------|
| App framework | Streamlit ≥ 1.32 |
| Data processing | Python, pandas ≥ 2.0, NumPy ≥ 1.24 |
| Spatial indexing | H3 ≥ 4.0 |
| Machine learning | scikit-learn ≥ 1.3 (RandomForestRegressor) |
| Visualisation | Plotly ≥ 5.18, Folium ≥ 0.15 + streamlit-folium ≥ 0.18 |

---

## Setup & Run

```bash
# 1. Clone the repository
git clone <repo-url>
cd theme1_prototype

# 2. Install dependencies
pip install -r requirements.txt

# 3. Launch the app
streamlit run app.py
```

All processed data files are already committed to `data/`. The app runs immediately without any additional preprocessing step.

To regenerate the data files from the raw enforcement CSV (requires the source dataset):

```bash
python preprocess_data.py
```

### Data files (in `data/`)

| File | Contents |
|------|----------|
| `cluster_stats.csv` | Per-H3-cell ZRI, spillover, persistence, zone type, and aggregate statistics |
| `station_summary.csv` | Violation counts and offender metrics per police station |
| `habitual_offenders.csv` | Repeat-offender watchlist with fine tiers and offense history |
| `hourly_by_station.csv` | Hourly violation counts per station (historical baseline) |
| `summary.json` | Top-level KPIs used on the Overview page |
| `predictor_model.pkl` | Trained RandomForest model for hourly forecasts |
| `predictor_metadata.json` | Feature names and model metadata |

---

## Data Note

The dataset covers **November 2023 – April 2024** (approximately 298,000 records) sourced from Bengaluru Traffic Police enforcement challan records. All vehicle registration numbers are anonymised. The dataset contains no speed-sensor readings; congestion impact estimates (travel-speed improvement percentages, violation-reduction projections) are modelled approximations, not measured outcomes.

---

## Limitations & Future Scope

**Current limitations**
- No live data feed; enforcement records are static (six-month snapshot).
- Congestion impact is estimated from traffic-engineering models, not measured with sensors.
- Safe Parking Finder reflects historical violation density, not real-time bay availability.
- SMS dispatch is a demo — no real gateway integration.

**Future scope**
- Real-time integration with Bengaluru Traffic Police's challan API.
- Speed and occupancy sensor feeds for direct congestion measurement.
- Expansion to other Karnataka cities.
- Mobile app for citizen reporting and officer guidance.
- Integration with BBMP parking management for live bay availability.

---

## Team

Built for Flipkart Gridlock Hackathon 2.0 — Round 2
