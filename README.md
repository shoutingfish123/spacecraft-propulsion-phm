# Hierarchical Fault Diagnosis of Spacecraft Propulsion Systems Using Physics-Informed Machine Learning

![Python](https://img.shields.io/badge/Python-3.8%2B-blue)
![Status](https://img.shields.io/badge/Status-Research%20Prototype-orange)
![Domain](https://img.shields.io/badge/Domain-Aerospace%20%7C%20PHM-red)

## 1. Overview
This repository contains a **Prognostics and Health Management (PHM)** framework designed for ISRO's next-generation spacecraft propulsion simulation system. The project moves beyond standard "black-box" classification by implementing a **hierarchical, physics-informed machine learning pipeline** to diagnose anomalies using high-frequency sensor data.

The system addresses the challenge of identifying **Bubble Contamination**, **Solenoid Valve Faults**, and **Unknown Anomalies** in a pressurized fluid network exhibiting complex fluid dynamics, including water hammer effects.

## 2. System Description
The experimental setup simulates a spacecraft propulsion system where water pressurized at **2.0 MPa** is discharged through four solenoid valves (SV1–SV4).

* **Sensors:** 7 Pressure Sensors (P1–P7) sampled at **1 kHz**.
* **Actuation:** Valves open at 100ms and close at 300ms.
* **Physics:** The system is characterized by pressure fluctuations due to the **water hammer effect** followed by acoustic mode propagation inside the piping.

## 3. Methodology
To handle the complexity of the 1 kHz time-series data, a hierarchical approach was adopted:

### Phase 1: Physics-Informed Feature Engineering
Instead of raw data, features were extracted based on the physical operation phases of the valves:
* **Time Segmentation:** Features extracted specifically from the "Water Hammer Window" (100ms–300ms) vs. steady state.
* **Statistical Moments:** Mean, Variance, Skewness, and Kurtosis to capture the shape of the pressure wave.
* **Differential Features:** Pressure drops across critical sections (e.g., $P_{tank} - P_{valve}$) to model flow resistance.

### Phase 2: Hierarchical Classification Pipeline
The problem was broken down into five sequential tasks to prevent class imbalance from skewing results:
1.  **Anomaly Detection:** Binary classification (Normal vs. Abnormal).
2.  **Fault Classification:** Distinguishing between Bubble vs. Valve Fault vs. Unknown.
3.  **Bubble Localization:** Identifying the specific bubble location (BP1–BP7).
4.  **Valve Localization:** Identifying the specific faulty valve (SV1–SV4).
5.  **Severity Estimation:** Regressing the valve opening ratio (0–100%).

**Key Algorithms:**
* **Random Forest & XGBoost:** For robust supervised classification.
* **Isolation Forest:** Implemented to detect "Unknown" system behaviors/outliers not seen in training.
* **GroupKFold Validation:** Utilized to ensure the model generalizes across different Spacecraft Units (Unit 1 vs. Unit 4) rather than overfitting to specific sensor noise.

## 4. Results & Performance
The model was evaluated on unseen test data (Spacecraft Unit 4), achieving an **Exact Row Match Accuracy of 76.02%**.

| Task | Description | Accuracy / Metric |
| :--- | :--- | :--- |
| **Task 1** | Anomaly Detection | **78.26%** |
| **Task 2** | Fault Type Classification | **76.09%** |
| **Task 3** | Bubble Localization | **70.00%** |
| **Task 4** | Valve Fault Localization | **20.00%** |
| **Overall** | **Exact Row Match** | **76.02%** |

*Note: The performance drop in Task 4 highlights the challenge of "Domain Shift" between spacecraft units, where subtle variations in piping geometry affect wave propagation.*

## 5. Future Work: Thesis Proposal
The current statistical approach struggles with **Task 4 (Valve Localization)** due to its inability to explicitly model the governing fluid equations.

My proposed Bachelor's Thesis will address this by implementing **Physics-Informed Neural Networks (PINNs)**. By embedding the **Navier-Stokes** and **Water Hammer equations** directly into the loss function, the model will be constrained to physically valid predictions, improving generalization across different spacecraft units without requiring extensive retraining.

## 6. Repository Structure
```bash
├── data/               # Processed datasets (GroundTruth.csv, Submission.csv)
├── notebooks/          # Jupyter notebooks for EDA and Modeling
│   ├── 01_Exploratory_Data_Analysis.ipynb
│   ├── 02_Feature_Engineering_Final.ipynb
│   └── 03_Final_Model.ipynb
├── docs/               # Project Report and Problem Statement
└── README.md           # Project Documentation
