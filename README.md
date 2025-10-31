# DA5401 A7: Multi-Class Model Selection using ROC and Precision-Recall Curves

## Author 

* **Name:** Nikhil Enugu
* **Roll No:** DA25M010

## 🎯 Objective
The primary goal is to compare a diverse set of classifiers on the **UCI Landsat Satellite dataset** (a 6-class problem) to determine the best-performing and worst-performing models.

The analysis goes beyond simple accuracy to evaluate model performance across different decision thresholds using:
* **Macro-Averaged ROC-AUC:** To measure overall class separability.
* **Macro-Averaged PRC-AP:** To measure performance on positive classes, which is more informative for imbalanced data.

## 📊 Dataset
* **Dataset:** [UCI Statlog (Landsat Satellite) Dataset](https://archive.ics.uci.edu/dataset/146/statlog+landsat+satellite)
* **Files:** `sat.trn` (training) and `sat.tst` (testing)
* **Citation:** Blake, C. and Merz, C.J. (1998). UCI Repository of machine learning databases. Irvine, CA: University of California, Department of Information and Computer Science.

## 🛠️ Methodology

1.  **Data Preparation:** Loaded the training and testing data. Features were standardized using `StandardScaler`.
2.  **Model Training:** Trained nine different classifiers on the scaled training data.
3.  **Baseline Evaluation:** Calculated Accuracy and Weighted F1-Score for all models.
4.  **ROC Analysis:** Plotted Macro-Averaged ROC curves (One-vs-Rest) and compared Macro-AUC scores.
5.  **PRC Analysis:** Plotted Macro-Averaged Precision-Recall curves (One-vs-Rest) and compared Average Precision (AP) scores.
6.  **Recommendation:** Synthesized all metrics to provide a final model recommendation.

### Models Compared
This analysis includes the required models plus "Brownie Points" models (Random Forest, XGBoost, and a custom anti-model).

**Top Performers:**
* Random Forest
* XGBoost
* Support Vector Machine (SVC)
* K-Nearest Neighbors (KNN)

**Mid-to-Low Performers:**
* Logistic Regression
* Decision Tree
* Naive Bayes (Gaussian)

**Baselines / Anti-Models:**
* **Dummy Classifier (`strategy='prior'`)**: The baseline for guessing based on class distribution.
* **INVERTED LINEAR SVC**: A custom-built "anti-model" designed to perform **worse than random** (AUC < 0.5) by intentionally inverting the calibrated probabilities of a strong `LinearSVC`.

## 📈 Results and Analysis

### Baseline Metrics (Accuracy & F1-Score)
While Random Forest had the highest F1-Score, ROC and PRC analysis revealed a more nuanced story. The `INVERTED LINEAR SVC` and `Dummy` classifiers performed as expected—at the very bottom.

### ROC Analysis (Macro-Averaged AUC)
ROC analysis shows the trade-off between True Positive Rate (TPR) and False Positive Rate (FPR). It's ideal for understanding overall model separability.

* **Best Model (AUC):** **XGBoost (AUC = 0.9902)**, showing near-perfect discrimination.
* **Worst Model (AUC):** **INVERTED LINEAR SVC (AUC = 0.0454)**. An AUC < 0.5 indicates the model is systematically *worse* than random guessing, which was its design goal.


### Precision-Recall Curve (PRC) Analysis (Macro-AP)
PRC is more informative for imbalanced datasets as it focuses on the performance of positive classes (Precision vs. Recall) by ignoring True Negatives.

* **Best Model (AP):** **XGBoost (AP = 0.9509)**, demonstrating the best balance of precision and recall.
* **Worst Model (AP):** **INVERTED LINEAR SVC (AP = 0.0908)**. The curve's sharp collapse shows it fails to maintain precision as it tries to find more positive samples (increasing recall).


### Metric-Ranking Synthesis
The rankings were highly consistent across all three metrics, with only a minor swap between the top two models.

| Model | **Weighted F1** | **ROC-AUC** | **PRC-AP** |
|:---|:---|:---|:---|
| **Random Forest** | **1 (0.9105)** | 2 (0.9900) | 2 (0.9507) |
| **XGBoost** | 2 (0.9030) | **1 (0.9902)** | **1 (0.9509)** |
| KNN | 4 (0.8943) | 3 (0.9848) | 3 (0.9325) |
| SVC | 5 (0.8925) | 4 (0.9852) | 4 (0.9176) |
| Decision Tree | 6 (0.8509) | 6 (0.9002) | 6 (0.7366) |
| Logistic Regression | 7 (0.8296) | 5 (0.9757) | 7 (0.8711) |
| Naive Bayes | 8 (0.8036) | 7 (0.9553) | 8 (0.8105) |
| Dummy | 9 (0.0864) | 8 (0.5000) | 9 (0.1667) |
| **INVERTED LINEAR SVC**| **10 (0.0000)** | **10 (0.0454)** | **10 (0.0908)** |

## 🏆 Final Recommendation

**The best model for this task is XGBoost.**

### Justification:
1.  **Dominates Threshold-Based Metrics:** It achieved the highest **ROC-AUC (0.9902)** for overall separability and the highest **PRC-AP (0.9509)** for precision-recall balance.
2.  **Robust Performance:** The PRC curve remains high and flat, indicating it maintains high precision even when recalling the majority of positive samples. This is critical for land cover mapping, where missing rare classes is costly.
3.  **Near-Top Practicality:** Its Weighted F1-Score (0.9030) was virtually tied with Random Forest, but its superiority in AUC/AP makes it more reliable across different decision thresholds.
