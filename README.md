[README.md](https://github.com/user-attachments/files/28919421/README.md)
<p align="center">
  <h1 align="center">🎓 Student Academic Risk Prediction</h1>
  <p align="center">
    <strong>Data Quality Analysis, Classification & K-Means Clustering on Synthetic University Data</strong>
  </p>
  <p align="center">
    <img src="https://img.shields.io/badge/Python-3.8-blue?style=for-the-badge&logo=python&logoColor=white" alt="Python">
    <img src="https://img.shields.io/badge/Random_Forest-Classifier-green?style=for-the-badge&logoColor=white" alt="RandomForest">
    <img src="https://img.shields.io/badge/K--Means-Clustering-purple?style=for-the-badge&logoColor=white" alt="KMeans">
    <img src="https://img.shields.io/badge/Pandas-Data-orange?style=for-the-badge&logo=pandas&logoColor=white" alt="Pandas">
    <img src="https://img.shields.io/badge/Jupyter-Notebook-red?style=for-the-badge&logo=jupyter&logoColor=white" alt="Jupyter">
  </p>
</p>

---

## ✨ About The Project

An end-to-end student analytics pipeline built on a **synthetic university dataset** — simulated behavioral, academic, and campus engagement records for 240 students. The project covers the full data science workflow: raw data cleaning, feature engineering, classification, and unsupervised clustering to identify at-risk students and drive data-informed academic support decisions.

> 💡 **Key Insight:** Despite a Random Forest accuracy of **94.7%**, severe class imbalance (only 12 At-Risk students out of 240) makes the model unreliable for real-world deployment without SMOTE or class weighting. Clustering revealed **4 distinct student profiles** that are far more actionable than a binary risk label.

---

## 📊 Quick Stats

<table>
  <tr>
    <td align="center"><b>🗂️ Raw Tables</b><br>5 interconnected CSVs</td>
    <td align="center"><b>👥 Students</b><br>240 unique records</td>
    <td align="center"><b>📅 Weekly Records</b><br>2,880 activity rows (12 weeks)</td>
    <td align="center"><b>🎯 Target</b><br>At-Risk vs Not-At-Risk</td>
  </tr>
  <tr>
    <td align="center"><b>🤖 Classifier</b><br>Random Forest (GridSearchCV)</td>
    <td align="center"><b>📈 CV Accuracy</b><br>94.7%</td>
    <td align="center"><b>🔵 Clusters</b><br>K = 4</td>
    <td align="center"><b>📆 Period</b><br>Feb 2025 – May 2025</td>
  </tr>
</table>

---

## 🎯 Student Clusters

| Cluster | Label | Avg. Study Hours | Avg. LMS Logins | Avg. Final Score |
|:---:|:---|:---:|:---:|:---:|
| 🏆 **1** | **High Engagement / High Performance** | Highest | Highest | Highest |
| 📚 **0** | **Campus Active / Good Performance** | Moderate | Moderate | High |
| ➡️ **2** | **Moderate Engagement / Average Performance** | Medium | Medium | Average |
| ⚠️ **3** | **Low Engagement / At-Risk** | Lowest | Lowest | Lowest |

> ⚠️ **Why cluster instead of just classify?**
> A binary risk label tells you *who* is struggling. Clustering tells you *why* — enabling targeted interventions: tutoring for Cluster 3, campus resource promotion for Cluster 0, and engagement campaigns for Cluster 2.

---

## 📐 Feature Dimensions

| Category | Features | Source Table |
|:---|:---|:---|
| **Behavioral** | study_hours, lms_logins, attendance_rate | weekly_activity |
| **Academic** | midterm_score, project_score, quiz_average | assessment |
| **Campus** | library_visits, advisor_meetings, club_participation | campus |
| **Demographic** | gender, department, year, scholarship_rate | students |
| **Target** | risk_status (At_Risk / Not_At_Risk) | grades |

---

## 🔧 Data Pipeline

```
📥 Raw Synthetic Data (5 CSVs, 240 students, 2,880 activity rows)
    │
    ├── 1️⃣  Standardize categorical labels
    │       └── gender (6 variants → M/F), department (6 variants → 4), club (6 variants → Yes/No)
    │
    ├── 2️⃣  Handle missing values
    │       ├── gender / department / club_participation → mode imputation
    │       └── study_hours / attendance_rate / quiz_average → median imputation
    │
    ├── 3️⃣  Detect & remove impossible values
    │       ├── attendance_rate > 1.0 or < 0.0 → NaN
    │       ├── lms_logins < 0 → NaN
    │       ├── study_hours > 40 (e.g. 80) → outlier, capped/removed
    │       └── scores > 100 or < 0 → NaN
    │
    ├── 4️⃣  IQR Outlier Detection
    │       └── study_hours, lms_logins, library_visits, advisor_meetings
    │
    ├── 5️⃣  Feature Engineering & Merging
    │       ├── Weekly activity → aggregated per student (mean)
    │       ├── One-Hot Encoding (gender, department, club_participation)
    │       └── Datetime parsing for week_start
    │
    ├── 6️⃣  Classification
    │       ├── Random Forest (GridSearchCV, 5-fold CV)
    │       ├── XGBoost (GridSearchCV, 5-fold CV)
    │       └── Best model: Random Forest (accuracy: 0.947)
    │
    └── 7️⃣  K-Means Clustering (K=4)
            └── StandardScaler → Silhouette + Inertia selection → Segment labeling
                │
                ▼
📊 Segmented Student Base → 💡 Targeted Academic Interventions
```

---

## 📈 Model Comparison

| Model | CV Accuracy | Notes | Selected |
|:---:|:---:|:---|:---:|
| **Random Forest** | **0.947** | Simpler, lower overfitting risk on small data | **✅ Best** |
| XGBoost | 0.947 | Equal accuracy, more complex tuning required | — |

> ⚠️ **Important caveat:** High accuracy is misleading here. With only **12 At-Risk students (5%)**, the model can predict "Not At-Risk" almost always and still look great. Future work should apply **SMOTE** or `class_weight='balanced'` to properly handle the imbalance.

---

## 📈 K Selection — Clustering Metrics

| K | Silhouette Score | Inertia | Selected |
|:---:|:---:|:---:|:---:|
| 2 | Higher | Higher | — |
| 3 | Moderate | — | — |
| **4** | **0.190** | **966** | **✅ Best balance** |
| 5 | Lower | Lower | — |

> K=4 provides the most **interpretable and actionable** segments. Each cluster maps to a distinct student behavioral profile that academic advisors can realistically act upon.

---

## 🔍 Key Correlations (Spearman)

| Feature | Correlation with Final Score | Interpretation |
|:---|:---:|:---|
| **study_hours_mean** | ~0.459 | Strongest predictor — consistent effort pays off |
| **midterm_score** | ~0.434 | Strong academic signal |
| **attendance_rate** | ~0.00 (near zero) | Surprisingly weak — possibly a synthetic data artifact |
| **quiz_average** | Negative in some pairs | Suspicious — likely a data generation artifact |

---

## 💡 Business Recommendations

| Cluster | Action | Expected Impact |
|:---|:---|:---|
| 🏆 High Engagement / High Performance | Recognize achievements, research opportunities | Retention & motivation |
| 📚 Campus Active / Good Performance | Leverage campus resources, peer mentoring roles | Community building |
| ➡️ Moderate Engagement / Average Performance | Engagement campaigns, study group invitations | Grade improvement |
| ⚠️ Low Engagement / At-Risk | Proactive advisor outreach, tutoring support | Churn reduction & early intervention |

> 🔐 **Ethical note:** Risk predictions should **only** trigger supportive outreach — never affect scholarship decisions or disciplinary records. Full transparency and student consent are essential for any real-world deployment.

---

## 🛠️ Technologies

<table>
  <tr>
    <td align="center"><img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/python/python-original.svg" width="40"/><br><b>Python</b></td>
    <td align="center"><img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/numpy/numpy-original.svg" width="40"/><br><b>NumPy</b></td>
    <td align="center"><img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/pandas/pandas-original.svg" width="40"/><br><b>Pandas</b></td>
    <td align="center"><img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/matplotlib/matplotlib-original.svg" width="40"/><br><b>Matplotlib</b></td>
    <td align="center"><img src="https://upload.wikimedia.org/wikipedia/commons/0/05/Scikit_learn_logo_small.svg" width="40"/><br><b>Scikit-learn</b></td>
    <td align="center"><img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/python/python-original.svg" width="40"/><br><b>XGBoost</b></td>
  </tr>
</table>

---

## 🚀 Installation & Usage

```bash
# 1. Clone the repository
git clone https://github.com/AloneMask001/Student-Academic-Risk-Prediction.git
cd Student-Academic-Risk-Prediction

# 2. Install dependencies
pip install numpy pandas matplotlib seaborn scikit-learn xgboost jupyter

# 3. Run the notebook
jupyter notebook AIN2002_HW4_2365109_SemihErdemVerep.ipynb
```

> ⚠️ **Note:** The notebook generates all synthetic CSV files automatically when run from top to bottom. No external dataset is required.

---

## 📁 Project Structure

```
Student-Academic-Risk-Prediction/
│
├── 📓 AIN2002_HW4_2365109_SemihErdemVerep.ipynb   # Main analysis notebook
├── 📊 hw4_students_raw.csv                         # Auto-generated: student demographics
├── 📊 hw4_weekly_activity_raw.csv                  # Auto-generated: 12-week behavioral data
├── 📊 hw4_assessment_raw.csv                       # Auto-generated: exam & quiz scores
├── 📊 hw4_campus_raw.csv                           # Auto-generated: campus engagement
├── 📊 hw4_grades_raw.csv                           # Auto-generated: final scores & risk labels
└── 📄 README.md                                    # Project documentation
```

---

## 👤 Developer

**Semih Erdem Verep**
2nd Year AI Engineering Student — AIN2002 Homework 4

---

## 📄 License

This project was developed for educational purposes as part of the AIN2002 coursework.
