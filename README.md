# Battery State of Health Prediction Using NMC/C+SiO Aging Data

## Overview

This project builds a machine learning pipeline to predict lithium-ion battery State of Health (SoH) using result data from a comprehensive NMC/C+SiO battery aging dataset.

The project was developed with support from an agentic AI workflow, which helped guide project planning, debugging, feature engineering, metric interpretation, and documentation. The main technical goal was to move from a basic EOCV-based battery health model to a stronger multi-source model using EOCV, EIS, and PLS/pulse-test features.

## Project Objective

The objective of this project was to:

- Build an end-to-end battery degradation modelling pipeline.
- Use EOCV, EIS, and PLS result data for SoH prediction.
- Engineer meaningful battery-health features.
- Train regression models on unseen cells.
- Evaluate model performance using MAE and RMSE.
- Compare how model accuracy improves as new feature groups are added.

## Dataset

The project uses a comprehensive lithium-ion NMC/C+SiO battery aging dataset containing approximately 228 cells tested under multiple calendar and cyclic aging conditions over roughly 600 days.

The raw dataset is not included in this repository because of file size. The project uses result-data files such as:

- EOCV result data
- EIS result data
- PLS / pulse-test result data

## Repository Structure

```text
battery-nmc-aging-project/
│
│
├── screenshots/
│   ├── example_file.png
│   ├── importance_frature_eis.png
│   ├── Screenshot 2026-06-03 183703.png
│   ├── Screenshot 2026-06-03 184035.png
│   ├── Screenshot 2026-06-03 192132.png
│   ├── stage1.png
│   ├── stage2_after_enrichment.png
│   ├── stage3_after_eis.png
│   └── stage4_eis_pls.png

Methodology
1. Data Loading And Initial Exploration
The first step was to inspect the downloaded result-data package and understand the available files. The data was organized into EOCV, EIS, and PLS result folders.



The data was loaded into pandas DataFrames and checked for usable identifiers such as cell_id and sd_block_id.

2. EOCV Feature Engineering
The first model used EOCV-based features to predict battery SoH. These features captured degradation behavior from capacity, aging step, temperature, SoC window, and related summary statistics.

Initial data loading and feature checks:



Additional feature preparation:



3. Baseline SoH Model
The first working SoH regression model was trained using EOCV-based features.

Result:

Model Stage	MAE	RMSE
First EOCV baseline	~0.44	~0.61
This gave the first proper benchmark for SoH prediction on unseen cells.



4. Enriched EOCV Model
The EOCV-only model was then improved by adding richer degradation features such as charge/energy throughput, temperature-related values, and additional aging descriptors.

Result:

Model Stage	MAE	RMSE
Enriched EOCV-only model	~0.42	~0.55
This showed that better battery-specific feature engineering improved prediction accuracy.



5. Adding EIS Features
The next step was to add EIS impedance-based features. These included cell-level impedance signatures such as impedance-based SoH indicators, reference impedance values, and mean impedance magnitude.

Feature importance from EIS-enhanced modelling:



Result:

Model Stage	MAE	RMSE
EOCV + EIS model	0.2814	0.3984
Adding EIS features significantly improved SoH prediction, reducing RMSE from about 0.55 to about 0.40.



6. Adding PLS / Pulse-Test Features
The final model added PLS and pulse-test resistance features. These features captured additional degradation signals that were not fully represented by EOCV or EIS alone.

Result:

Model Stage	MAE	RMSE
EOCV + EIS + PLS model	0.2584	0.3674
This was the best-performing instantaneous SoH model.



Final Model Comparison
Model Stage	MAE	RMSE	Observation
First EOCV baseline	~0.44	~0.61	First working SoH model
Enriched EOCV-only model	~0.42	~0.55	Improved with better feature engineering
EOCV + EIS model	0.2814	0.3984	Major improvement from impedance features
EOCV + EIS + PLS model	0.2584	0.3674	Best final model
The final model reduced RMSE from approximately 0.55 to 0.37 SoH percentage points on unseen cells.

Early-Life Prediction Experiment
In addition to instantaneous SoH prediction, an early-life prediction experiment was performed using only the first 10-30% of each cell's life to predict final SoH.

Early-Life Fraction	MAE	RMSE
First 10% of life	5.942	8.028
First 20% of life	6.762	8.693
First 30% of life	6.783	8.469
These results show that long-horizon final-SoH prediction is much harder than instantaneous SoH estimation.



Key Results
Built a complete battery SoH prediction pipeline using EOCV, EIS, and PLS result data.
Improved SoH prediction RMSE from approximately 0.55 to 0.37.
Achieved final model performance of:
MAE: 0.2584
RMSE: 0.3674
Demonstrated that EIS impedance features provide strong predictive value for battery health estimation.
Added PLS / pulse-test features for further improvement.
Ran early-life prognostics experiments to evaluate long-horizon battery health prediction.
Tools And Libraries
Python
pandas
NumPy
scikit-learn
XGBoost
matplotlib
Jupyter Notebook
Challenges And Fixes
During the project, several practical issues were identified and corrected:

Fixed incorrect directory paths while locating result and log data.
Identified that the available .tar file contained result data rather than raw 2-second log data.
Avoided duplicated rows while merging EIS features by checking cell_id and sd_block_id.
Fixed RMSE calculation issues caused by changes in the scikit-learn API.
Focused the project scope on result-data modelling instead of very large raw time-series logs.
