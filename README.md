# Hospital Stay Prediction

## 📌 Project Overview

This project focuses on predicting a patient's **length of hospital stay** using hospital, patient, and admission-related information.

The target variable contains **11 length-of-stay categories**, ranging from short stays such as `0-10 days` to longer stays such as `More than 100 Days`.

The project covers the complete machine learning workflow:

- Data understanding and cleaning
- Exploratory Data Analysis (EDA)
- Data-quality validation
- Hypothesis testing
- Feature engineering
- Handling missing values and outliers
- Categorical encoding
- Model training and comparison
- Class-imbalance handling
- Model evaluation
- Feature-importance analysis
- Model serialization and deployment validation

---

## 🎯 Objective

The main objective is to build a machine learning model that can predict the expected **hospital length-of-stay category** from information available at the time of admission.

Such predictions can support:

- Hospital resource planning
- Bed allocation
- Staffing decisions
- Capacity planning
- Operational decision support

> **Note:** The model is intended as an operational decision-support tool and should not replace clinical judgment.

---

## 📊 Dataset

The dataset contains approximately **318,000 hospital admission records** and includes features related to:

- Hospital characteristics
- Hospital region
- Department
- Ward and bed grade
- Patient age
- Patient city
- Severity of illness
- Admission type
- Number of visitors
- Admission deposit
- Length of stay

### Target Variable

`Stay` — categorical length-of-stay range.

The target contains **11 classes**.

---

## 🔎 Data Quality & Preprocessing

Several data-quality and preprocessing challenges were identified and handled.

### Excel Date-Conversion Issue

During data validation, the intended category:

```text
11-20
```

was found to have been silently converted by Excel into:

```text
20-Nov
```

This occurred in the `Stay` and `Age` columns.

The corrupted value was identified and corrected back to `11-20` before continuing with the analysis.

### Missing Values

Missing values were handled using **mode imputation** for relevant categorical variables, including:

- `Bed_Grade`
- `City_Code_Patient`

### Outliers

Extreme values in numerical variables such as:

- `Admission_Deposit`
- `Visitors_with_Patient`

were handled using **IQR-based outlier capping**.

### Encoding

Different encoding strategies were applied according to feature type:

- Ordinal encoding for ordered categories
- Label encoding for selected coded categorical variables
- One-hot encoding for nominal categorical variables

### Multicollinearity

Correlation and multicollinearity checks were performed.

Redundant one-hot encoded feature pairs were removed where appropriate.

---

## 🧪 Exploratory Data Analysis

The project includes univariate, bivariate, and multivariate analysis to understand:

- Target-class distribution
- Patient demographics
- Hospital characteristics
- Admission patterns
- Relationships between predictors and length of stay
- Potential outliers and skewness
- Class imbalance

Statistical hypothesis testing was also performed using tests such as:

- Chi-Square test
- Kruskal-Wallis test

These tests were used to investigate relationships between selected predictors and the target.

---

## ⚖️ Class Imbalance

The target variable is highly imbalanced, meaning some length-of-stay categories contain significantly more observations than others.

This creates a challenge because a model may perform well on common classes while performing poorly on rare classes.

To address this:

- Class imbalance was analyzed explicitly.
- Balanced weighting strategies were tested.
- Fully-balanced XGBoost sample weights were evaluated.
- A **softly-balanced XGBoost** approach using the square root of balanced sample weights was also evaluated.

The softly-balanced approach produced the strongest reported overall performance.

---

## 🤖 Models Evaluated

The project compares multiple machine learning approaches.

### 1. Logistic Regression

Used as a baseline linear classification model.

### 2. Random Forest

Used as a non-linear tree-based model and evaluated in both baseline and tuned forms.

### 3. XGBoost

Used as a powerful gradient-boosted tree model.

Different XGBoost approaches were evaluated, including:

- Baseline XGBoost
- Tuned XGBoost
- Fully-balanced XGBoost
- Softly-balanced XGBoost
- Ordinal-regression-style XGBoost

---

## 📈 Model Performance

The strongest reported results are:

| Model | Accuracy | Weighted F1 |
|---|---:|---:|
| Logistic Regression | 24.57% | 0.2604 |
| Random Forest (tuned) | 36.71% | 0.3618 |
| XGBoost (tuned) | 30.00% | 0.3107 |
| **XGBoost (softly-balanced)** | **40.97%** | **0.3819** |

### 🏆 Final Model

**Softly-Balanced XGBoost**

- Accuracy: **40.97%**
- Weighted F1: **0.3819**

It is selected because it achieved the highest reported Accuracy and Weighted F1 among the evaluated models.

---

## ❓ Why are the Accuracy and F1 Scores Relatively Low?

The performance should be interpreted in the context of this particular problem.

The model is predicting **11 different length-of-stay classes**, rather than a simple binary outcome.

The main reasons for the moderate scores are:

1. **Strong class imbalance**  
   Some stay-duration categories have far fewer records than others.

2. **Multiple classes**  
   The model must distinguish between several neighboring stay-duration ranges.

3. **Class overlap**  
   Patients with similar admission characteristics can still have different actual lengths of stay.

4. **Limited available information**  
   The available hospital and patient features cannot capture every clinical, treatment, and operational factor that affects length of stay.

5. **Weighted F1 considers precision and recall**  
   Therefore, it is more informative than simply counting correct predictions, especially for an imbalanced multi-class problem.

A moderate Accuracy score therefore does not automatically indicate a coding error or a failed model. The model should be evaluated comparatively and with additional class-level metrics such as Macro F1.

---

## 🔍 Feature Importance

Feature importance is analyzed using the **final softly-balanced XGBoost model**.

The leading predictors in the current model include:

- `Admission_Deposit`
- `Visitors_with_Patient`
- `Age_ordinal`
- `City_Code_Patient`
- `Available_Extra_Rooms_in_Hospital`

Feature importance describes how the model uses variables for prediction. It does **not** imply that a feature causes a longer or shorter hospital stay.

Model-based feature importance is used primarily for interpretation rather than automatically dropping features before evaluation.

---

## 🚀 Deployment

The final XGBoost model is serialized using `joblib`.

The deployment workflow includes:

1. Train the final model
2. Save the trained model
3. Reload the saved model
4. Generate predictions using the reloaded model
5. Compare the predictions with those from the original model

The notebook confirms that the saved and reloaded model produces matching predictions on held-out, already-preprocessed samples.

### Production Consideration

For a complete production deployment, the preprocessing pipeline should also be packaged with the model.

This should include:

- Missing-value handling
- Outlier treatment
- Categorical encoding
- Feature construction
- Feature ordering/schema

This ensures that new raw patient records are transformed exactly as they were during model training.

---

## 🏭 Production Recommendation

The recommended production candidate is:

> **Softly-Balanced XGBoost**

It is recommended because it currently provides the best combination of Accuracy and Weighted F1 among the evaluated models.

Before live deployment, the following steps are recommended:

- Package the complete preprocessing and model pipeline
- Validate on an independent or future dataset
- Monitor class-level performance
- Monitor prediction/data drift
- Version the model and feature schema
- Periodically retrain when data distribution changes
- Use predictions as decision support rather than clinical decision-making

---

## 🛠️ Technologies Used

- Python
- Pandas
- NumPy
- Matplotlib
- Seaborn
- Scikit-learn
- XGBoost
- SciPy
- Joblib
- Jupyter Notebook

---

## 📁 Suggested Repository Structure

```text
hospital-stay-prediction/
│
├── README.md
├── House_Stay_Prediction_Solution.ipynb
│
├── models/
│   ├── hospital_stay_prediction_model.joblib
│   └── hospital_stay_model_artifacts.joblib
│
├── reports/
│   ├── Model_Comparison_Performance_Production_Report.docx
│   └── Challenges_Faced_and_How_Handled.docx
│
└── data/
    └── README.md
```

> Do not upload sensitive patient information or confidential datasets to a public GitHub repository.

---

## 📌 Key Challenges

The major challenges addressed in this project were:

- Excel-induced data corruption
- Severe target-class imbalance
- Multi-class length-of-stay prediction
- Missing values
- Numerical outliers
- Mixed categorical and numerical variables
- Redundant encoded features
- Non-linear relationships
- Model-selection trade-offs
- Moderate predictive performance
- Feature-importance methodology
- Deployment and preprocessing consistency

---

## 📌 Key Takeaways

- Data-quality validation was essential because an Excel date-conversion issue corrupted one of the categorical values.
- Class imbalance significantly affects multi-class hospital-stay prediction.
- Tree-based models performed better than the linear baseline.
- Soft class balancing improved XGBoost performance compared with the other evaluated approaches.
- The final model is **softly-balanced XGBoost**.
- Final reported performance is **40.97% Accuracy** and **0.3819 Weighted F1**.
- Production deployment should package preprocessing and the model together.

---

## 👤 Project Status

**Status:** Completed model-development and evaluation phase

**Final Model:** Softly-Balanced XGBoost

**Best Reported Accuracy:** 40.97%

**Best Reported Weighted F1:** 0.3819

**Deployment Validation:** Model save/load prediction consistency verified

---

## 📄 License

This project is intended for educational and portfolio purposes. Add an appropriate license here if the repository is intended for public distribution.
