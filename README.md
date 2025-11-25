# Comparative Statistical Analysis of River Flow Volumes

![Python](https://img.shields.io/badge/Python-3.9%2B-blue)
![Status](https://img.shields.io/badge/Status-Completed-success)
![Domain](https://img.shields.io/badge/Domain-Hydrology%20%2F%20Time%20Series-green)

## 📊 Executive Summary

This project implements a quantitative framework to estimate the **withdrawable water volumes** from river systems under regulatory constraints (Ecological Flow / *Deflusso Ecologico*). 

The tool performs a comparative analysis between two statistical methodologies often used in hydrological risk assessment:
1.  **Multi-Year Stochastic Approach:** Analyzes the full historical time series to derive volume distributions.
2.  **Typical Year Probabilistic Approach:** Constructs synthetic "Mean" and "Poor" (20th percentile) years based on daily flow duration curves (FDC).

> **Note:** This repository contains the source code for my Bachelor's Thesis in Civil Engineering at Politecnico di Torino. The core logic demonstrates transferrable skills in time-series analysis, numerical integration, and statistical modeling.

---

## 🖼️ Visualization

### Flow Duration Curve & Volume Estimation
The algorithm computes the withdrawable volume by integrating the area between the available flow curve (depurated of ecological constraints) and the plant capacity.

![Duration Curve Example](https://github.com/user-attachments/assets/c9170f90-c9bf-441f-bfc6-1c00082e3beb)
*(Example: Duration curve showing Available Flow vs. Maximum Withdrawable Flow)*

---

## 🧮 Mathematical Framework

The core challenge is to model the random variable $Q_{river}(t)$ and apply a non-linear constraint $DE(t)$ (Ecological Flow) to find the net available resource.

### 1. Ecological Flow Constraint ($DE$)
The model implements the regional regulatory formula (DPGR 28/12/2021) as a dynamic threshold:

$$
DE = k \cdot q_{meda} \cdot S \cdot M \cdot A \cdot Z \cdot T
$$

Where $T$ is a time-varying parameter based on seasonality, and $q_{meda}$ represents the specific mean discharge.

### 2. Withdrawable Flow ($Q_{deriv}$)
For any given day $t$, the withdrawable flow is a non-linear function defined by the physical constraint of the plant ($Q_{max}$) and the regulatory minimum ($DE$):

$$
Q_{deriv}(t) = \min \left( \max(Q_{river}(t) - DE(t), 0) , \ Q_{max} \right)
$$

### 3. Numerical Integration
The total withdrawable volume $V$ for a reference period is computed using the **Trapezoidal Rule** over the discrete time series:

$$
V = \int_{0}^{T} Q_{deriv}(t) \, dt \approx \sum_{i=1}^{N-1} \frac{Q_{deriv}(t_{i+1}) + Q_{deriv}(t_i)}{2} \cdot \Delta t
$$

---

## 🛠️ Technical Implementation

The project is structured in a single Jupyter Notebook performing an End-to-End analysis:

1.  **Data Ingestion & Cleaning:**
    * Parsing raw CSV data from ARPA Piemonte stations.
    * Handling missing values and date formatting.
    * **Leap Year Adjustment:** Removal of Feb 29th to standardize yearly analysis.
2.  **Parameter Mapping:**
    * Implementation of dictionary-based mapping for station-specific hydro-geological parameters ($k$, $S$, $M$, etc.).
3.  **Algorithm Design:**
    * **`daily_withdrawal(df)`**: Vectorized calculation of daily margins.
    * **`poor_mean_year(df)`**: Statistical construction of synthetic years using quantile sorting and aggregation.
    * **`mean_withdrawable_volume(df)`**: Calculation of volumes via numerical integration (`np.trapz`).
4.  **Visualization:**
    * Comparison of distributions using Box Plots (Seaborn).
    * Time series and Duration Curves plotting (Matplotlib).

### Tech Stack
* **Python:** Core logic.
* **Pandas:** Time series manipulation and aggregation.
* **NumPy:** Vectorized calculations and numerical integration.
* **Matplotlib / Seaborn:** Statistical data visualization.

---

## 🚀 Getting Started

### Prerequisites
* Python 3.x
* Jupyter Notebook

### Installation
1.  Clone the repository:
    ```bash
    git clone [https://github.com/matteoientile/Tesi.git](https://github.com/matteoientile/Tesi.git)
    ```
2.  Install dependencies:
    ```bash
    pip install pandas numpy matplotlib seaborn
    ```
3.  Run the notebook:
    ```bash
    jupyter notebook TESI-Cod_DEFINITIVO.ipynb
    ```

---

## 📈 Results

The analysis highlights significant differences between the Multi-Year approach and the Typical Year approach. The comparison shows that:
* The **Multi-Year approach** provides a more robust estimation of the variance and tail risks (extreme drought years).
* The **Typical Year approach**, while computationally lighter, tends to smooth out volatility, potentially underestimating risks in highly variable regimes.

![Box Plot Comparison](https://github.com/user-attachments/assets/8953594c-fa2c-4df0-bd9f-2b0e83de1e51)
