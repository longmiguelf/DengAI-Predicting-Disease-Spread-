# DengAI: Predicting Disease Spread 🦟📊

**Author:** Alexander Miguel Long Ferreira  
**Contact:** longmiguelf@gmail.com 

## 📌 Project Overview
This repository contains a comprehensive machine learning approach to predict the number of Dengue fever cases in two distinct cities: San Juan (Puerto Rico) and Iquitos (Peru). The project is based on the DrivenData "DengAI: Predicting Disease Spread" competition. 

Given the drastically different climate dynamics and epidemiological behaviors between the two locations, the data for each city was modeled independently. The repository is divided into two main phases: Unsupervised Learning (clustering environmental regimes) and Supervised Learning (predictive modeling of outbreak cases).

---

## 📂 Phase 1: Unsupervised Learning (Environmental Clustering)
The goal of this phase was to discover hidden climate patterns and environmental regimes that could act as a basis for early warning systems.

### Methodology & Preprocessing
*   **Dimensionality Reduction:** Applied PCA and t-SNE, which confirmed a natural separation of the data by city without explicitly feeding the `city` feature to the models.
*   **Algorithms Evaluated:** K-Means, Hierarchical Clustering, DBSCAN, and Gaussian Mixture Models (Expectation Maximization).

### Key Findings
*   **Optimal Clusters:** The data naturally separates into **2 distinct climate regimes in San Juan** (likely representing dry and wet seasons) and **3 regimes in Iquitos** (reflecting higher climate complexity).
*   **Best Model:** **Gaussian Mixture Models (GMM)** outperformed other methods by providing highly reliable probabilistic assignments. It achieved high confidence (>0.9 probability) for 89.5% of San Juan's data and 94.6% of Iquitos' data.
*   **Limitations:** DBSCAN was discarded due to the "curse of dimensionality" (25 variables), which resulted in classifying 46% to 100% of the observations as noise.

---

## 📈 Phase 2: Supervised Learning (Time-Series Prediction)
The objective of the second phase was to build a robust predictive model capable of estimating weekly dengue cases (`total_cases`) based on historical climate and vegetation data.

### Feature Engineering & Preprocessing
*   **Handling Missing Data:** Applied linear temporal interpolation for critical variables like `ndvi_ne` and a KNN Imputer (K=5) for the remaining missing values to preserve multivariate relationships.
*   **Feature Creation:** Created 4-week and 8-week moving averages for key climate variables (temperature, specific humidity, and precipitation) to account for the mosquito's life cycle and the incubation period of the virus.
*   **Outlier Strategy:** Outliers in `total_cases` were intentionally kept as they represent actual severe epidemic outbreaks, which are the main events of interest.

### Modeling & Evaluation
To prevent data leakage, model evaluation was strictly performed using **TimeSeriesSplit** (5 folds) for cross-validation.

*   **Models Tested:** Random Forest, XGBoost, Support Vector Regressor (SVR), and AdaBoost.
*   **Hyperparameter Tuning:** Conducted using `RandomizedSearchCV` followed by `GridSearchCV`.
*   **Results:** While the SVR model achieved the best internal cross-validation score (MAE ~15.10), it severely overfitted the training data and failed to capture explosive epidemic peaks on the hidden test set (MAE ~30.00).
*   **Final Selected Model:** **XGBoost (Extreme Gradient Boosting)** was selected as the final production model due to its superior generalization capabilities and better bias-variance trade-off on unseen data. It achieved the best final competition score with a **Test MAE of 24.4832**.

---

## 🚀 Future Work
To further improve predictive performance in future iterations, the following strategies are proposed:
*   **Advanced Feature Engineering:** Incorporate explicit time lags for both climate variables and historical dengue cases to better capture temporal autocorrelation.
*   **Ensemble Stacking/Voting:** Combine predictions from XGBoost, AdaBoost, and Random Forest using a Voting Regressor to stabilize predictions and minimize extreme errors.
*   **Bayesian Optimization:** Replace Random/Grid Search with Bayesian optimization libraries (such as Optuna) for more efficient and precise hyperparameter tuning.

---
*Developed with Python, Pandas, Scikit-Learn, XGBoost, Seaborn, and Matplotlib.*
