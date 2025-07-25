# End-to-End Driver Churn Prediction Pipeline Using Azure ML and Ensemble Models

---

## 1. Project Overview

In this project, I developed a scalable and accurate driver churn prediction solution for Ola using Azure Machine Learning Studio. The goal was to predict driver attrition based on driver demographics, income, tenure, and performance data from 2019 to 2020, supporting retention and operational strategies.

The solution includes data preprocessing, feature engineering, automated model training with Azure AutoML, ensemble modeling, explainability, and a deployment-ready pipeline. While the model has not been deployed in production yet to avoid unnecessary costs, the pipeline and artifacts are fully compatible with Azure ML deployment infrastructure for future use.

---

## 2. Data Preparation & Feature Engineering

- Missing values in numerical and binary features were imputed using KNNImputer (k=5).
- Data was aggregated at the driver level (Driver_ID) to create features such as:
  - QuarterlyRating_Increased: Whether the driver’s quarterly rating improved over time.
  - Income_Increased: Whether income increased during the observed period.
- Mode imputation was applied for categorical variables like Gender, City, Education, and Designation.
- The final cleaned and feature-engineered dataset was exported as `driver_attrition_ml_table.csv` for modeling.

---

## 3. Modeling Details

I utilized Azure AutoML to automate model training and selected a weighted soft voting ensemble model based on **highest accuracy**.

| Component              | Details                                                       |
|------------------------|---------------------------------------------------------------|
| Modeling Approach      | Azure AutoML with Voting Ensemble (5 base models ensembled)    |
| Ensemble Strategy     | Weighted soft voting, final model selected on highest accuracy |
| Base Models Used      | XGBoostClassifier, LightGBM (two variants), RandomForest, ExtremeRandomTrees |
| Model Weights         | XGBoost: 0.40, LightGBM: 0.33, RandomForest: 0.13, ExtremeRandomTrees: 0.067 each |
| Best Individual Model | Iteration 1 (XGBoost) with Accuracy: 97.1%                     |
| Pipeline Type         | AutoML-generated pipeline with MeanCrossValidation             |

---

## 4. Model Hyperparameters

| Model             | learning_rate | n_estimators | max_depth | min_child_weight | subsample | colsample_bytree | gamma | num_leaves | min_child_samples | min_samples_split | min_samples_leaf | bootstrap | criterion |
|-------------------|---------------|--------------|-----------|------------------|-----------|------------------|-------|------------|-------------------|-------------------|------------------|-----------|-----------|
| XGBoostClassifier | 0.1           | 100          | 6         | 1                | 0.8       | 0.8              | 0     | –          | –                 | –                 | –                | –         | –         |
| LightGBM_1        | 0.05          | 200          | -1        | –                | 0.7       | 0.9              | –     | 31         | 20                | –                 | –                | –         | –         |
| RandomForest      | –             | 100          | None      | –                | –         | –                | –     | –          | –                 | 2                 | 1                | True      | gini      |
| ExtremeRandomTrees| –             | 100          | None      | –                | –         | –                | –     | –          | –                 | 2                 | 1                | False     | gini      |
| LightGBM_2        | 0.03          | 150          | 10        | –                | 0.8       | 0.8              | –     | 40         | 30                | –                 | –                | –         | –         |

---

## 5. Performance Metrics

| Metric                | Value     |
|-----------------------|-----------|
| Accuracy              | 97.1%     |
| AUC (Macro)           | 0.994     |
| Log Loss              | 0.084     |
| Matthews Corr. Coef.  | 0.936     |
| Precision (Weighted)  | 97.2%     |
| F1 Score (Macro)      | 0.967     |
| Recall (Macro)        | 0.975     |
| Balanced Accuracy     | 97.5%     |
| Sensitivity (Recall)  | 97.1%     |
| Specificity           | (if available) |
| Confusion Matrix      | Available in project reports |

---

## 6. Explainability & Deployment

- Model explainability is enabled using Azure Responsible AI, facilitating interpretation and trust.
- The model pipeline and artifacts are fully prepared for deployment, including a Conda environment (`conda_env.yml`), `model.pkl`, and scoring script.
- While I have not deployed the model yet to avoid incurring additional costs, it is ready for seamless deployment on Azure ML real-time endpoints as needed.

---

## 7. Compute and Infrastructure

- Training was performed on Azure Machine Learning Compute Cluster, providing scalable managed VMs.
- The design supports deployment on serverless real-time endpoints, enabling automatic scaling and minimal infrastructure management.

---

## 8. Business Insights for Ola

- The high accuracy and recall of the model enable early identification of drivers likely to churn, allowing Ola to implement timely retention measures.
- Drivers showing no income growth or decline in quarterly ratings are at higher risk of churn, suggesting key performance and incentive areas to monitor.
- Proactive churn prediction supports efficient fleet management by anticipating driver availability and reducing operational disruptions.

---

## 9. Repository Contents

| File/Folder                  | Description                                                                 |
|------------------------------|-----------------------------------------------------------------------------|
| `README.md`                  | Overview of the project, objectives, setup instructions, and results.       |
| `PROJECT_REPORT.md`          | Detailed report covering data insights, modeling pipeline, metrics, and business recommendations. |
| `LICENSE`                    | MIT license for open-source usage and distribution rights.                  |
| `driver_attrition_ml_table.csv` | Final feature-engineered dataset used for model training (do not upload if private). |
| `feature_engineering.py`     | Modular script for preprocessing, feature engineering, and target creation. |
| `automl_driver.py`           | Azure AutoML training script using ensemble learning techniques.            |
| `model.pkl`                  | Serialized trained model (Voting Ensemble) ready for deployment.            |
| `script.py`                  | Scoring script for Azure ML real-time endpoint deployment.                  |
| `conda.yaml`                 | Environment specification used in Azure ML for training/inference.          |
| `requirements.txt`           | List of Python packages for local use or reproducibility.                   |
