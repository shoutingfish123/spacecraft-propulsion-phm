# Hierarchical Fault Diagnosis of Spacecraft Propulsion Systems Using Physics-Informed Machine Learning

![Python](https://img.shields.io/badge/Python-3.8%2B-blue)
![Status](https://img.shields.io/badge/Status-Research%20Prototype-orange)
![Domain](https://img.shields.io/badge/Domain-Aerospace%20%7C%20PHM-red)

## 1. Overview
This repository contains a **Prognostics and Health Management (PHM)** framework designed for a spacecraft's propulsion simulation system. The project incorporates a **hierarchical, physics-informed machine learning pipeline** to diagnose anomalies using high-frequency sensor data.

The system addresses the challenge of classifying three major faults that arise in spacecraft propulsion sytem, namely **Bubble Contamination**, **Solenoid Valve Faults**, and **Unknown Anomalies** in a pressurized fluid network exhibiting complex fluid dynamics, including water hammer effects.

## 2. System Description
The experimental setup simulates a spacecraft propulsion system where water pressurized at 2.0 MPa is discharged through four solenoid valves (SV1–SV4).

* **Sensors:** 7 Pressure Sensors (P1–P7) sampled at 1 kHz, for a period of 1.2s.
* **Actuation:** Valves opens and closes once every 400ms, thereby opens and closes thrice during the enitre 1.2s period.
* **Physics:** The system is characterized by pressure fluctuations due to the water hammer effect followed by acoustic mode propagation inside the piping.
<p align="center">
  <img width="350" height="182" alt="image" src="https://github.com/user-attachments/assets/75d8ecb1-d069-4e55-a919-9d54c6727975" />
</p>




## 3. Dataset Description
The  training dataset consists of 177 csv files of pressure fluctuation in the 7 pressure sensors for a period of 1.2s, sampled
at 1 kHz  for three spacecraft (Spacecraft 1, 2, and 3). The test dataset consists of 46 csv files belonging to a known Spacecraft 1 and a completely unknown Spacecraft 4.

## 4. Methodology
### Exploratory Data Analysis
On subtracting the mean normal signal from that of an abnormal, high frequency residuals are obtained, indicating that fault information
is hidden in the frequency domain (acoustics), rather than in global statistical shifts. Visualization of pressure variation further showed that fault detection can be done on the dataset by analyzing individual pressure fluctuations with time.
<p align="center">
<img width="398" height="145" alt="image" src="https://github.com/user-attachments/assets/a81b0e87-3e7e-453d-9602-d15a7834f2a0" />
</p>



### Physics-Informed Feature Engineering
Instead of using standard time domain features like mean, median, skew etc. for feature engineering, this project involves a Physics-Informed feature engineering approach:
* **Time Segmentation:** The 1.2s period is divided into 3 equal parts from which maximum standard deviation of pressure is calculated. This prevents the model from ignoring local extrema in search of global extrema which are not always indicative of fault.- **Frequency-Domain Analysis:** To capture the acoustic variations that may arise in the system, the Fast Fourier Transform (FFT) is applied to convert data from the time domain to the frequency domain and to obtain:
  - **Dominant Frequency Power:**  Captures the magnitude of the primary resonance.
  - **Energy Bins:** Energy is separated into:
    - Low-frequency (0–50 Hz), which represents the water hammer thud  
    - High-frequency (> 50 Hz), which represents acoustic ringing


### Hierarchical Classification Pipeline
The problem was broken down into five sequential tasks to prevent class imbalance from skewing results:
1.  **Abnormal Datapoint Detection:** Binary classification (Normal vs. Abnormal).
2.  **Fault Classification:** Distinguishing between Bubble vs. Valve Fault vs. Unknown.
3.  **Bubble Localization:** Identifying the specific bubble location (BP1–BP7).
4.  **Valve Localization:** Identifying the specific faulty valve (SV1–SV4).
5.  **Severity Estimation:** Regressing the valve opening ratio (0–100%).

**Key Algorithms:**
* **KNN, Random Forest:** For robust supervised classification.
* **Isolation Forest:** Implemented to detect "Unknown" system behaviors/outliers not seen in training.
* **GroupKFold Validation:** Utilized to ensure the model generalizes across different Spacecraft Units (Unit 1 vs. Unit 4) rather than overfitting to specific sensor data.

## 4. Results & Performance
The model was evaluated on unseen test data (Spacecraft Unit 4), achieving an Exact Row Match Accuracy of **76.02%**.

Description | Accuracy / Metric |
:--- | :--- |
Fault Detection | 78.26% |
Fault Type Classification | 76.09% |
Bubble Localization | 70.00% |
Valve Fault Localization | 20.00% |
Exact Row Match | **76.02%** |

*Note: The performance drop in Valve Fault Localization highlights the challenge of "Domain Shift" as the model is tested on unseen data from Spacecraft 4.*



## 6. Repository Structure
```bash
├── data/               # Processed datasets (GroundTruth.csv, ModelResult.csv)
├── notebooks/          # Jupyter notebooks for EDA and Modeling
│   ├── 01_Exploratory_Data_Analysis.ipynb
│   ├── 02_Feature_Engineering_Final.ipynb
│   └── 03_Final_Model.ipynb
├── docs/               # Project Report and Problem Statement
└── README.md           # Project Documentation
