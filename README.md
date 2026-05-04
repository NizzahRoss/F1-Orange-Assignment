Orange Workflow Guide – F1 Race Result Classification
Practical Assignment 2
Files you need (keep both in the same folder):
`f1\_orange.tab` — the dataset in Orange format (12,228 rows, 8 columns)
`f1\_assignment.ows` — the pre-built workflow
---
Quick Start
Open Orange 3
File → Open → select `f1\_assignment.ows`
Double-click the "F1 Dataset" File widget → click the folder icon → choose `f1\_orange.tab` → Apply
All widgets will populate automatically
---
Dataset Summary (for your report)
Property	Value
Source	Formula 1 World Championship (Ergast API / Kaggle)
Era covered	1994–2024 (modern era)
Total data objects	12,228 race entries
Target variable	`result\_class` (class role)
Features	7 (all set to "feature" role)
Missing values	0
Duplicate rows	0
Features table
Feature	Type	Role	Description	Range
grid	continuous	feature	Starting grid position	1 – 28
laps	continuous	feature	Laps completed in race	0 – 87
year	continuous	feature	Championship season	1994 – 2024
round	continuous	feature	Race round within season	1 – 24
circuit_enc	discrete	feature	Circuit ID (label-encoded)	0 – 76
constructor_enc	discrete	feature	Constructor/team (label-encoded)	0 – 209
driver_enc	discrete	feature	Driver (label-encoded)	0 – 856
result_class	discrete	class	Race outcome category	4 classes
Classes
Class	Meaning	Count	%
Points	Finished P4–P10	3,955	32.3%
DNF	Did not finish (mechanical/accident)	3,388	27.7%
Outside_Points	Finished P11+	3,155	25.8%
Podium	Finished P1–P3	1,730	14.1%
---
Part I – Step-by-Step Configuration
File Widget (already loads automatically after you select the file)
In Orange's File widget, verify: 8 columns visible, `result\_class` shows Role = class
Info panel should show: 12228 instances, 7 features, 1 class
Column Statistics
Double-click → view mean, std, min, max, median for all features
For report: record central tendency (mean/median) and dispersion (std, range) for `grid` and `laps`
Scatter Plot 1 – "Scatter: Grid vs Laps"
Double-click → set:
X axis: `grid`
Y axis: `laps`
Color: `result\_class`
Observation: DNF entries cluster at low laps; Podium entries cluster at low grid positions
Scatter Plot 2 – "Scatter: Grid vs Round"
Double-click → set:
X axis: `grid`
Y axis: `round`
Color: `result\_class`
Bar Plot 1 – "Bar Plot – Grid by Class"
Double-click → set:
X axis: `result\_class`
Y axis: `grid` (mean)
Or switch to show counts grouped by class
Bar Plot 2 – "Bar Plot – Laps by Class"
Double-click → set:
X axis: `result\_class`
Y axis: `laps` (mean)
Distributions
Double-click → select `grid` from the attribute list
Enable "Split by class" to see separation
Switch to `laps` for the second distribution
Box Plot
Double-click → set variable to `grid`, group by `result\_class`
Repeat for `laps`
---
Part II – Unsupervised ML Configuration
Distances Widget
Double-click → Metric: Euclidean, Normalize: checked
Connected to: File (input) → Hierarchical Clustering (output)
Hierarchical Clustering
Experiment 1: Linkage = Ward, drag cut-off line to get k=2 clusters → note contents
Experiment 2: Same Ward linkage, drag to k=4 → compare to true 4 classes
Experiment 3: Same Ward linkage, drag to k=6 → observe over-splitting
> For each experiment: screenshot the dendrogram + note the silhouette score shown
k-Means Widget
Double-click → settings:
✅ Optimize k (from 2 to 8)
Init: k-Means++
n Init: 10
Max iter: 300
The widget will show a bar chart of silhouette scores per k automatically
Connect to Silhouette Plot widget to see the silhouette plot for the selected k
Experiments to document (change k manually for each):
k	Silhouette (approx)
2	0.229
3	0.179
4	0.199
5	0.192
6	0.194
> Best silhouette is at k=2, but k=4 aligns best with our 4 true classes.
---
Part III – Supervised ML Configuration
Data Sampler (Train/Test Split)
Double-click → settings:
✅ Fixed proportion of data: 80%
✅ Stratified
✅ Fixed random seed: 42
Output: "Data Sample" = training (9,782 rows), "Remaining Data" = test (2,446 rows)
---
Algorithm 1: kNN
Experiment 1 (baseline):
n_neighbors = 3, weights = Uniform, metric = Euclidean
Experiment 2 (distance weighting):
n_neighbors = 7, weights = Distance, metric = Euclidean
Experiment 3 ⭐ (best):
n_neighbors = 11, weights = Distance, metric = Manhattan
Expected Test CA ≈ 0.700
---
Algorithm 2: Neural Network (MLP)
Experiment 1 (shallow):
Hidden layers: 100
Activation: ReLU, Solver: Adam, α = 0.0001, Max iter = 200
Experiment 2 (two layers):
Hidden layers: 100, 50
Activation: ReLU, Solver: Adam, α = 0.001, Max iter = 300
Experiment 3 ⭐ (best):
Hidden layers: 100, 50 (same architecture, better regularisation)
Activation: ReLU, Solver: Adam, α = 0.001, Max iter = 300, Early stopping: ✅
Expected Test CA ≈ 0.722
---
Algorithm 3: Random Forest
Experiment 1 (shallow):
Number of trees: 50, Max depth: 5, Min samples per leaf: 5
Experiment 2 (deeper):
Number of trees: 100, Max depth: 10, Min samples per leaf: 2
Experiment 3 ⭐ (best):
Number of trees: 200, Max depth: unlimited, Min samples per leaf: 1
Expected Test CA ≈ 0.740
---
Test and Score Widget
Double-click → set Resampling: Cross-validation, k = 5 folds, ✅ Stratified
This evaluates all 3 learners on the training set simultaneously
Screenshot the results table showing CA, AUC, F1 for each algorithm
Confusion Matrix
Double-click → select algorithm from dropdown
Screenshot for each of the 3 algorithms (train + test)
Predictions Widget (Test Set evaluation)
Connected to: Remaining Data (from Data Sampler) + all 3 learners
Double-click → shows predicted class for each test row alongside actual class
Use the Confusion Matrix (Test) widget connected below for the final test confusion matrix
---
Expected Results Summary
Algorithm	Train CA (5-fold CV)	Test CA
kNN (k=11)	~0.70	~0.700
Neural Network (100,50)	~0.74	~0.722
Random Forest (200 trees)	~0.74	~0.740 ✅ best
Best model: Random Forest — highest test accuracy with balanced precision/recall across all 4 classes
---
Report Conclusions (template answers)
Part I conclusions
Are classes balanced?
Moderately balanced. Points (32%), DNF (28%), Outside_Points (26%), Podium (14%). The Podium class is underrepresented (~half the size of others) but not severely imbalanced.
Does visual representation show structure?
Partially. The `laps` feature clearly separates DNF from the three finishing classes (DNF entries have far fewer laps completed). Grid position partially separates Podium entries (concentrated at positions 1–6). Points and Outside_Points overlap significantly.
How many data groupings can be identified visually?
2–3 natural groupings are visible: DNF (low laps), front-runners (low grid + high laps), and a merged group of mid-field/back-of-grid finishers.
Are identified groupings close or far apart?
DNF is clearly separated from finishers via the `laps` axis. The three finishing classes (Podium, Points, Outside_Points) are close together and partially overlapping — class boundaries are not crisp.
Part II conclusions
The low silhouette scores (0.18–0.23) for both algorithms indicate that the dataset does not form tight, well-separated natural clusters. This is consistent with Part I observations: while DNF entries are visually distinct, the three finishing classes (Podium, Points, Outside_Points) overlap substantially. Hierarchical clustering at k=4 does not recover the true class boundaries cleanly. The dataset classes are poorly separable by unsupervised methods alone.
Part III conclusions
Random Forest (74.0% test accuracy) outperforms kNN (70.0%) and Neural Network (72.2%). All models achieve high precision for the DNF class (≥94%) but struggle to distinguish Points from Outside_Points, which is expected given their visual overlap. The Neural Network provides a good balance between training generalization and test performance, showing less overfitting than kNN and Random Forest.
