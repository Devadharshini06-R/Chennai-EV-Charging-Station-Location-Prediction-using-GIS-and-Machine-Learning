# Chennai EV Charging Station Location Prediction using GIS and Machine Learning

## Project Overview

This project focuses on identifying and analysing potential locations for future Electric Vehicle (EV) charging stations in Chennai using Geographic Information System (GIS) and Machine Learning (ML) techniques.

The project combines spatial analysis in QGIS with Python-based Machine Learning to identify high-suitability areas, generate candidate locations, analyse their characteristics, and rank them based on their suitability and potential.

The project is designed as a decision-support analysis for future EV charging infrastructure planning.

## Objectives

- Identify suitable areas for future EV charging stations in Chennai.
- Analyse population distribution.
- Analyse road accessibility.
- Combine spatial factors using GIS weighted overlay.
- Generate high-suitability zones.
- Generate candidate locations from suitable areas.
- Extract GIS-derived features for candidate locations.
- Apply Machine Learning techniques.
- Classify candidate locations based on potential.
- Identify natural groups of candidate locations.
- Calculate a priority score.
- Rank candidate locations for further consideration.

## Overall Project Workflow

```text
Data Collection
       ↓
Data Preprocessing
       ↓
QGIS Spatial Analysis
       ↓
Population Analysis
       ↓
Road Accessibility Analysis
       ↓
Population Score + Road Score
       ↓
Weighted Overlay
       ↓
EV Suitability Map
       ↓
High-Suitability Areas
       ↓
Candidate Point Generation
       ↓
GIS Feature Extraction
       ↓
Python Data Processing
       ↓
Machine Learning
       ├── Random Forest
       └── K-Means Clustering
       ↓
Priority Score
       ↓
Candidate Ranking
       ↓
Final Results
```

## Data Used

### Population Data

WorldPop 2025 population raster for Chennai, at approximately 100 m resolution.

Population values were converted into suitability scores from 1 to 5.

```text
1 → Very Low
2 → Low
3 → Medium
4 → High
5 → Very High
```

### Road Network

The Chennai road/highway network was used for accessibility analysis and nearest-road distance calculation.

The project uses:

```text
EPSG:32644
WGS 84 / UTM Zone 44N
```

### Existing EV Charging Stations

Existing EV charging station locations were used as spatial reference data. They can also be used as an independent target in future versions.

### Chennai Boundary

The Chennai boundary defines the study area. Relevant raster and vector layers were clipped to the study area.

### Points of Interest

Additional POIs explored include:

- Shopping malls
- Supermarkets
- Fuel stations
- Metro stations

These can be incorporated as additional features in future versions.

# GIS Methodology

## 1. Population Analysis

The population raster was processed in QGIS and converted into a 1–5 suitability score.

```text
Higher Population
        ↓
Higher Population Score
        ↓
Potentially Higher EV Demand
```

## 2. Road Accessibility Analysis

Distance from each raster cell to the nearest road was calculated and converted into a 1–5 road accessibility score.

```text
Closer to Road
      ↓
Better Accessibility
      ↓
Higher Road Suitability
```

## 3. Weighted Overlay

Population Score and Road Score were combined using equal weights:

```text
EV Suitability Score
=
(Population Score × 0.5)
+
(Road Score × 0.5)
```

Therefore:

```text
Population Weight = 50%
Road Weight       = 50%
```

Example:

```text
Population Score = 5
Road Score       = 4

Suitability = (5 × 0.5) + (4 × 0.5)
             = 4.5
```

## 4. High-Suitability Areas

The suitability raster was used to identify high-suitability areas. The high-suitability range used in the project was 4–5.

A high-suitability area does not mean that a charging station must definitely be constructed there. It represents a location that is suitable according to the factors included in this analysis.

## 5. Candidate Location Generation

Candidate points were generated inside the high-suitability areas.

**Total candidate locations: 254**

For each candidate, GIS-derived values such as population, population score, road score, suitability score and coordinates were extracted.

The candidate dataset was exported to CSV for Python and Machine Learning analysis.

# Python Data Processing

Python was used for:

- Data loading
- Data cleaning
- Feature preparation
- Machine Learning
- Clustering
- Model evaluation
- Candidate ranking

Main libraries:

- Pandas
- NumPy
- Scikit-learn
- Matplotlib

# Machine Learning Methodology

Two Machine Learning approaches were used:

1. Random Forest
2. K-Means Clustering

## 1. Random Forest

Random Forest was used as a supervised classification model.

### Input Features

GIS-derived features such as:

```text
Population Score
Road Score
Population Value
Suitability Score
```

### Target

```text
High   → 1
Medium → 0
```

### Configuration

```text
Number of Trees = 200
Maximum Depth = 10
Class Weight = Balanced
Train/Test Split = 80/20
```

### Output

The model produces a predicted class and prediction probability.

## Random Forest Evaluation

The current experiment produced:

```text
Accuracy  = 100%
Precision = 1.00
Recall    = 1.00
F1 Score  = 1.00
```

This result must be interpreted carefully because the High/Medium target was derived from GIS suitability information that was also included among the model features. Therefore, the 100% test accuracy mainly indicates that Random Forest learned the existing GIS-based classification rule and should not be interpreted as 100% real-world prediction accuracy.

## 2. K-Means Clustering

K-Means was used as an unsupervised Machine Learning technique.

Features used:

```text
Population Score
Population Value
Suitability Score
```

The features were standardized before clustering.

Number of clusters:

```text
3
```

The cluster with the highest population and suitability characteristics was interpreted as the high-potential cluster.

The K-Means silhouette score was approximately:

```text
0.746
```

# Candidate Priority Ranking

A priority score was calculated using:

- Population Score
- Suitability Score
- Normalized Population Value

Current formula:

```text
Priority Score =
(0.4 × Population Score)
+
(0.3 × Suitability Score)
+
(0.3 × Normalized Population Value × 5)
```

Candidate locations were then ranked from 1 to 254.

The ranking identifies candidate locations with stronger characteristics according to the factors included in the current analysis.

# Final Output

```text
254 Candidate Locations
        ↓
GIS Suitability Analysis
        ↓
High-Suitability Areas
        ↓
Candidate Feature Dataset
        ↓
Random Forest Classification
        ↓
K-Means Clustering
        ↓
Priority Score
        ↓
Ranked Candidate Locations
```

The final output can be used to identify locations that may deserve further investigation for future EV charging infrastructure.

# What Does "High Potential" Mean?

In this project, High Potential means that the candidate location has strong characteristics according to the selected GIS and Machine Learning analysis.

It does NOT mean:

- A charging station will definitely be installed there.
- The location is commercially guaranteed.
- The location is the only suitable location.
- The model guarantees future EV demand.

The output should be considered a GIS and Machine Learning based decision-support result.

# Project Limitations

1. **Target Leakage:** The current Random Forest target was derived from GIS suitability information that is also used as model input.
2. **Limited Features:** The current suitability model primarily focuses on population and road accessibility.
3. **Real EV Demand:** Actual EV charging demand is not directly included.
4. **Land Availability:** Land availability is not directly considered.
5. **Electricity Infrastructure:** Grid capacity and electricity availability are not included.
6. **Cost:** Installation and operational costs are not included.

# Future Work

## Phase 1 — Independent ML Target

Use existing EV charging station data as an independent real-world target instead of deriving the target directly from the GIS suitability score.

Current approach:

```text
GIS Suitability
      ↓
High / Medium Label
      ↓
Machine Learning
```

Future approach:

```text
Real EV Station Data
      ↓
Independent Target
      ↓
Machine Learning
```

## Phase 2 — Add More GIS Features

Potential features include:

- Distance to malls
- Distance to supermarkets
- Distance to fuel stations
- Distance to metro stations
- Distance to existing EV stations
- Road density
- Traffic volume
- Commercial land use
- Parking availability

## Phase 3 — Improve Model Validation

Future versions can use:

- Spatial Cross-Validation
- ROC-AUC
- Precision
- Recall
- F1 Score
- Confusion Matrix

Spatial validation is particularly important because nearby geographic locations can have similar characteristics.

## Phase 4 — Compare Machine Learning Models

Future work can compare:

```text
Random Forest
XGBoost
LightGBM
Logistic Regression
Gradient Boosting
K-Means
DBSCAN
```

## Phase 5 — Update the Analysis with New Data

```text
New Population Data
        +
Updated Road Network
        +
Updated EV Stations
        +
New EV Demand Data
        ↓
Updated GIS Analysis
        ↓
Updated ML Model
        ↓
Updated Candidate Ranking
```

# Technologies Used

### GIS
- QGIS
- Raster Analysis
- Vector Analysis
- Raster Distance
- Weighted Overlay
- Spatial Sampling
- Coordinate Reference Systems

### Programming
- Python
- Pandas
- NumPy
- Scikit-learn
- Matplotlib

### Machine Learning
- Random Forest
- K-Means Clustering
- Feature Scaling
- Classification
- Clustering
- Ranking

# Recommended Repository Structure

```text
Chennai-EV-Charging-GIS-ML/
│
├── README.md
│
├── data/
│   ├── raw/
│   └── processed/
│
├── notebooks/
│   ├── 01_Data_Preparation.ipynb
│   ├── 02_GIS_Feature_Extraction.ipynb
│   ├── 03_Random_Forest.ipynb
│   ├── 04_KMeans_Clustering.ipynb
│   └── 05_Final_Ranking.ipynb
│
├── qgis/
│   └── Chennai_EV_GIS_Project.qgz
│
├── outputs/
│   ├── EV_Candidate_Sampled.csv
│   ├── ML_Predictions.csv
│   └── Final_Ranked_Candidates.csv
│
├── requirements.txt
│
└── LICENSE
```

# Reproducibility

To reproduce the project:

1. Collect the required Chennai spatial datasets.
2. Prepare and clip datasets to the Chennai study area.
3. Create the population suitability score.
4. Calculate road distance and create the road accessibility score.
5. Perform weighted overlay.
6. Extract high-suitability areas.
7. Generate candidate locations.
8. Extract raster values for each candidate.
9. Export the candidate dataset to CSV.
10. Run the Python Machine Learning workflow.
11. Apply Random Forest classification.
12. Apply K-Means clustering.
13. Calculate the priority score.
14. Rank candidate locations.
15. Use the final ranked dataset for further analysis and future model improvements.

# Conclusion

This project demonstrates the integration of GIS and Machine Learning for EV charging infrastructure planning in Chennai.

GIS was used to identify spatially suitable areas based on population and road accessibility. A weighted overlay approach was used to create an EV suitability map, and high-suitability areas were converted into candidate locations.

A total of 254 candidate locations were then analysed using Random Forest and K-Means.

Random Forest was used for classification and probability estimation, while K-Means was used to identify natural groups among candidate locations.

Finally, a priority score was calculated to rank candidate locations.

The project provides a foundation for a more advanced EV charging site recommendation system using additional real-world data and improved Machine Learning validation.

# Author

**Devadharshini Radhakrishnan**

### Areas of Interest

- Data Analytics
- Machine Learning
- GIS
- Python
- Artificial Intelligence
- Data Visualization

---

> This project is intended as a decision-support analysis for potential EV charging locations in Chennai and not as a definitive infrastructure deployment recommendation.
