# DWDM Assignment – Data Mining Experiments using Orange

**Subject:** Data Warehousing and Data Mining (DWDM)
**Tool:** [Orange Data Mining](https://orangedatamining.com/) (Orange 3.x)
**Name:** Richard Joshva Y  |  **Reg. No:** RA2411027020160

---

## Table of Contents

1. [Data Cleaning](#1-data-cleaning)
2. [Data Reduction](#2-data-reduction)
3. [Data Transformation](#3-data-transformation)
4. [Association Rules (Apriori)](#4-association-rules-using-apriori)
5. [Statistical Analysis](#5-statistical-analysis)
6. [Regression](#6-regression)
7. [Outlier Analysis](#7-outlier-analysis)
8. [k-Means on Iris](#8-k-means-clustering-on-iris)
9. [Clustering Rules on iris.arff (Simple k-Means)](#9-clustering-rule-process-on-irisarff-simple-k-means)

---

## Setup

1. Download and install Orange from <https://orangedatamining.com/download/>.
2. Open Orange → **Options → Add-ons** and install **Associate** (needed for Experiment 4).
3. Build each workflow on the canvas, save it as `.ows`, and put it in the `workflows/` folder.
4. Save screenshots of each workflow and result in the `images/` folder (e.g., `exp1_workflow.png`, `exp5_output.png`).

### Suggested repository structure

```
DWDM-Orange-Assignment/
├── README.md
├── datasets/            # iris.arff, iris.csv, heart_disease.tab, etc.
├── workflows/           # exp1_data_cleaning.ows ... exp9_clustering_rules.ows
└── images/              # exp1_workflow.png, exp1_output.png ...
```

---

## 1. Data Cleaning

**Aim:** Clean a dataset by handling missing values, duplicates and inconsistent data using Orange.

**Dataset:** `heart_disease` (built-in; contains missing values). Load via **File** or **Datasets** widget.

**Widgets used:** File, Data Table, Feature Statistics, Impute, Select Rows, Unique, Edit Domain, Save Data

**Workflow:**
`File → Data Table (raw)` → `Feature Statistics` → `Impute` → `Unique` → `Select Rows` → `Data Table (clean)` → `Save Data`

**Procedure:**
1. Drag a **File** widget onto the canvas and load the dataset.
2. Connect **Data Table** and inspect rows; note the cells with missing values (shown as `?`).
3. Connect **Feature Statistics** to see the count/percentage of missing values per attribute.
4. Add **Impute**: choose *Average/Most frequent* (numeric/categorical), or *Model-based (Simple Tree)*, or *Remove rows with missing values*.
5. Add **Unique** to remove duplicate rows.
6. Add **Select Rows** to remove invalid values (e.g., age < 0).
7. Use **Edit Domain** to fix attribute names/types or merge inconsistent category labels.
8. Compare the raw and cleaned **Data Table** and export with **Save Data**.

**Result:** Missing values replaced, duplicates removed, and inconsistent values fixed; the cleaned dataset is ready for mining.

**Screenshot (workflow + Data Table output):**

![Experiment 1 – Data Cleaning workflow](images/exp1_workflow.png)

---

## 2. Data Reduction

**Aim:** Reduce dataset size/dimensionality while keeping the important information.

**Dataset:** `wine` or `iris` (built-in).

**Widgets used:** File, Data Sampler, Rank, Select Columns, Correlations, PCA, Scatter Plot, Data Table

**Workflow:**
`File → Data Sampler → Data Table` (numerosity reduction)
`File → Rank → Select Columns → Data Table` (feature selection)
`File → PCA → Scatter Plot` (dimensionality reduction)

**Procedure:**
1. **Sampling:** Connect **Data Sampler**, choose *Fixed proportion of data* (e.g., 70%) and view the sample in a Data Table.
2. **Feature selection:** Connect **Rank** and score attributes (Information Gain, Gain Ratio, Gini, ReliefF, χ²). Keep top-ranked attributes.
3. Use **Correlations** to find highly correlated attributes and drop redundant ones with **Select Columns**.
4. **PCA:** Connect **PCA**, inspect the scree plot, choose components that explain ~95% variance, and check the transformed data in a Scatter Plot.

**Result:** Fewer rows (sampling) and fewer attributes (feature selection/PCA) with minimal information loss.

**Screenshot (File → PCA → Data Table, showing PC1 and PC2):**

![Experiment 2 – Data Reduction workflow](images/exp2_workflow.png)

---

## 3. Data Transformation

**Aim:** Transform data into forms suitable for mining (normalization, discretization, continuization, new features).

**Dataset:** `iris` or `housing` (built-in).

**Widgets used:** File, Preprocess, Normalize, Discretize, Continuize, Feature Constructor, Transpose, Data Table

**Workflow:**
`File → Normalize → Data Table`
`File → Discretize → Data Table`
`File → Continuize → Data Table`
`File → Feature Constructor → Data Table`

**Procedure:**
1. **Normalization:** Use **Normalize** (or **Preprocess → Normalize Features**) with *Standardize to μ=0, σ²=1* or *Normalize to interval [0,1]*.
2. **Discretization:** Use **Discretize** with *Equal-width* or *Equal-frequency* binning (e.g., 3 bins) to turn numeric attributes into categories.
3. **Continuization:** Use **Continuize** to convert categorical attributes into numeric (one-hot / indicator) columns.
4. **Feature construction:** Use **Feature Constructor** to create a new attribute, e.g. `petal_area = petal_length * petal_width`.
5. Optionally apply **Transpose** to swap rows and columns.
6. Compare the Data Tables before and after each step.

**Result:** Data is scaled, binned, encoded and enriched with new features, ready for downstream algorithms.

**Screenshot (File → Preprocess → Data Table):**

![Experiment 3 – Data Transformation workflow](images/exp3_workflow.png)

---

## 4. Association Rules using Apriori

**Aim:** Generate frequent itemsets and association rules.

**Dataset:** `zoo` (built-in) or any market-basket dataset in one-hot (yes/no) form.

**Add-on required:** Associate

**Widgets used:** File / Datasets, Frequent Itemsets, Association Rules, Data Table

**Workflow:**
`File → Frequent Itemsets → Association Rules → Data Table`

**Procedure:**
1. Load the dataset in **File** (attributes must be categorical/binary; discretize numeric ones first).
2. Connect **Frequent Itemsets** and set the *minimal support* (e.g., 30%).
3. Connect **Association Rules** and set *minimal confidence* (e.g., 60%). Optionally filter by lift.
4. Inspect rules in the table: **Support**, **Confidence**, **Lift**, **Leverage**, **Coverage**.
5. Select a rule to see the matching itemsets/rows.

**Note:** Orange's Associate add-on uses the FP-growth technique to find frequent itemsets. It yields the same frequent itemsets and rules as the classic Apriori algorithm, which is why it is used for this experiment.

**Result:** Rules of the form `{A, B} → {C}` with their support, confidence and lift.

📷 `images/exp4_workflow.png`, `images/exp4_rules.png`

---

## 5. Statistical Analysis

**Aim:** Demonstrate descriptive and relational statistics on a dataset.

**Dataset:** Palmer Penguins (344 rows; bill length, bill depth, flipper length, body mass; grouped by species).

**Widgets used:** File, Feature Statistics, Distributions, Box Plot, Correlations, Scatter Plot, Contingency Table / Mosaic Display

**Workflow:**
`File → Feature Statistics`
`File → Distributions`
`File → Box Plot`
`File → Correlations → Scatter Plot`

**Procedure:**
1. **Feature Statistics:** view mean, median, mode, minimum, maximum, dispersion and missing values.
2. **Distributions:** view the histogram of each attribute, optionally split by class.
3. **Box Plot:** show median, quartiles and outliers per class; read the statistical test result (ANOVA / t-test) shown under the plot.
4. **Correlations:** compute Pearson or Spearman correlation between all attribute pairs.
5. **Scatter Plot:** visualize the strongest correlated pair (e.g., petal length vs petal width).

**Result:** Summary statistics, distributions and correlations that show which attributes separate the classes best (for penguins: flipper length and body mass are strongly correlated, r = 0.87, and body mass differs significantly between species, ANOVA F = 338.6, p < 0.001; Gentoo is the heaviest).

**Output (feature statistics, correlation matrix, box plot):**

![Experiment 5 – Statistical Analysis output](images/exp5_output.png)

---

## 6. Regression

**Aim:** Build and evaluate regression models to predict a numeric target.

**Dataset:** Auto-MPG (398 cars; target = `mpg`; predictors include weight, horsepower, displacement, model year), 80/20 train–test split.

**Widgets used:** File / Datasets, Data Sampler, Linear Regression, Random Forest, kNN, Test and Score, Predictions, Scatter Plot

**Workflow:**
`File → Data Sampler → (Linear Regression, kNN, Random Forest) → Test and Score`
`Data Sampler → Predictions ← Linear Regression`

**Procedure:**
1. Load the dataset and make sure the target variable is numeric.
2. Use **Data Sampler** to split into training (e.g., 70%) and test (30%) data.
3. Add **Linear Regression** (no regularization / Ridge / Lasso) and, for comparison, **kNN** and **Random Forest**.
4. Connect all to **Test and Score** (cross-validation or test on test data).
5. Compare **MSE, RMSE, MAE and R²** — a lower error and higher R² means a better model.
6. Use **Predictions** to compare actual and predicted values and visualize them in a Scatter Plot.

**Result:** The best model is chosen by lowest RMSE/MAE and highest R² (here, multiple linear regression beats simple regression: R² 0.825 vs 0.723, RMSE 3.072 vs 3.859, MAE 2.455 vs 3.118 on the test set; weight and horsepower have negative coefficients).

**Output (simple vs multiple linear regression, test scores, coefficients):**

![Experiment 6 – Regression output](images/exp6_output.png)

---

## 7. Outlier Analysis

**Aim:** Detect outliers using Orange.

**Dataset:** Auto-MPG (398 cars), using Box Plot (IQR), Z-score and the Orange **Outliers** widget (Covariance Estimator).

**Widgets used:** File, Outliers, Select Rows, Scatter Plot, Box Plot, Data Table

**Workflow:**
`File → Outliers → (Inliers, Outliers) → Data Table / Scatter Plot`
`File → Box Plot`

**Procedure:**
1. Load the dataset in **File**.
2. Connect **Outliers** and choose a method: *One-class SVM*, *Covariance estimator*, *Local Outlier Factor* or *Isolation Forest*.
3. Set the *contamination* (expected % of outliers, e.g., 10%).
4. View the **Outliers** and **Inliers** outputs in separate Data Tables.
5. Plot the data in **Scatter Plot** with the `Outlier` column as color to see where outliers lie.
6. Confirm with **Box Plot** (points beyond the whiskers).

**Result:** Outlying records are identified and can be removed (using Inliers output) or investigated. In the output, 20 of 398 records were flagged; horsepower had the most outliers (11 by IQR, 5 with |Z| > 3), followed by acceleration (7 and 2), mpg (1 and 0) and weight (0 and 0).

**Output (box plot, flagged outliers, outliers per feature):**

![Experiment 7 – Outlier Analysis output](images/exp7_output.png)

---

## 8. k-Means Clustering on Iris

**Aim:** Implement the k-means algorithm on the Iris dataset.

**Dataset:** Wine (178 instances, standardized) as used in the output below; the same steps apply to `iris`.

**Widgets used:** File, Select Columns, k-Means, Silhouette Plot, Scatter Plot, Data Table, Confusion Matrix / Mosaic Display

**Workflow:**
`File → k-Means → (Scatter Plot, Data Table, Silhouette Plot)`

**Procedure:**
1. Load `iris` in **File**. (Optionally move `iris` class to *meta* with **Select Columns** so clustering is unsupervised.)
2. Connect **k-Means**; use *Initialization: k-means++* and *Normalize columns* checked.
3. Set *Number of clusters* to **From 2 to 8**. The widget shows the **silhouette score** for each k; choose the best k (k = 3 matches the three species).
4. Output the data with a **Cluster** column to a **Data Table**.
5. Plot in **Scatter Plot** with *Color = Cluster* (and *Shape = iris*) to view the clusters.
6. Use **Silhouette Plot** to inspect how well each point fits its cluster.
7. Compare clusters against the real species using **Mosaic Display** or **Distributions**.

**Result:** The silhouette score is highest at k = 3 (about 0.28), matching the three true cultivars. Compared with the true classes, class_0 → C3 (59), class_1 → C1 (65, with 3 in C2 and 3 in C3) and class_2 → C2 (48).

**Output (silhouette scores, k = 3 clusters on PCA, clusters vs true class):**

![Experiment 8 – k-Means output](images/exp8_output.png)

---

## 9. Clustering Rule Process on iris.arff (Simple k-Means)

**Aim:** Demonstrate the clustering process on `iris.arff` using simple k-means and derive rules that describe each cluster.

**Dataset:** `iris.arff` (copy from Weka's `data` folder, or use the built-in Orange `iris`). The output below was produced on `wine.arff` (k = 3).

> **Note:** If your Orange version cannot open `.arff` through the File widget, convert it to `.csv` (e.g., open in Weka → Save as CSV) and load that, or use Orange's built-in `iris`.

**Widgets used:** File, Select Columns, k-Means, Data Table, Scatter Plot, CN2 Rule Induction, CN2 Rule Viewer, Mosaic Display

**Workflow:**
`File → k-Means → Data Table → Select Columns (Target = Cluster) → CN2 Rule Induction → CN2 Rule Viewer`
`k-Means → Scatter Plot`

**Procedure:**
1. Load `iris.arff` in **File**.
2. Connect **k-Means** with **k = 3**, *k-means++* (simple k-means), and a fixed maximum number of iterations.
3. View cluster assignments (Cluster C1, C2, C3) in **Data Table** and visualize with **Scatter Plot**.
4. Connect **Select Columns**: set `Cluster` as the **target**, remove the original class from features/target.
5. Connect **CN2 Rule Induction** to the output to learn IF–THEN rules for each cluster.
6. Open **CN2 Rule Viewer** to read the rules, e.g.:
   `IF petal length ≤ 2.5 THEN Cluster = C1`
7. Compare clusters to the actual iris classes with **Mosaic Display** to find the percentage of *incorrectly clustered instances*.

**Result:** Three clusters with centroid-based assignments and readable rules that explain which attribute ranges define each cluster. For the Wine output below: SimpleKMeans with k = 3, seed 10, min-max normalised; 7 iterations, within-cluster sum of squared errors 19.4911, cluster sizes 50 / 70 / 58 (28% / 39% / 33%), and 7 incorrectly clustered instances (3.93%) in the classes-to-clusters evaluation. Exact values depend on the run and settings.

**Output (Weka-style SimpleKMeans summary, centroids, cluster plot):**

![Experiment 9 – Simple k-Means output](images/exp9_output.png)

---

## Conclusion

All nine data mining tasks — cleaning, reduction, transformation, association rule mining, statistical analysis, regression, outlier analysis and clustering (k-means and rule-based) — were demonstrated visually using Orange workflows, without writing code.

## References

- Orange Data Mining: <https://orangedatamining.com/>
- Orange Widget Catalog: <https://orangedatamining.com/widget-catalog/>
- Orange Associate add-on: <https://orange3-associate.readthedocs.io/>
- Han, J., Kamber, M., Pei, J., *Data Mining: Concepts and Techniques*.
