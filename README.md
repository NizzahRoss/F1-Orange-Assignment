# F1 Race Result Classification - Orange Workflow

Short description of what the project is.

## Files
Explain:
- `f1_assignment.ows` = Orange workflow
- `f1_orange.tab` = dataset
- `README.md` = instructions

## Requirements
- Orange 3
- No Python needed
- No extra libraries needed

## How to Download
Include:
- Download ZIP steps
- Or Git clone command

## How to Open the Project
Steps:
1. Open Orange 3
2. File → Open
3. Select `f1_assignment.ows`

## How to Load the Dataset
Explain:
1. Open the File widget
2. Select `f1_orange.tab`
3. Click Apply

Mention that `.ows` and `.tab` must be in the same folder.

## Dataset
Only brief info:
- Dataset is Formula 1 race data
- Target variable is `result_class`
- Classes: `Podium`, `Points`, `Outside_Points`, `DNF`

## Workflow Overview
Mention the workflow has:
1. Data loading and visualisation
2. Unsupervised learning
3. Supervised learning

## How to Run Visualisation
Explain which widgets to open:
- Data Table
- Column Statistics
- Scatter Plot
- Bar Plot
- Distributions
- Box Plot

## How to Run Clustering
Explain:
- Open k-Means
- Try different `k` values
- Check Silhouette Plot
- Open Hierarchical Clustering if needed

## How to Run Classification Models
Explain:
- Open Data Sampler
- Use 80/20 train/test split
- Open kNN, Neural Network, Random Forest
- Open Test and Score
- Compare results

## How to Check Results
Explain:
- Use Test and Score for metrics
- Use Confusion Matrix for mistakes
- Use Predictions for row-by-row predictions

## Troubleshooting
Include:
- dataset not loading
- widgets empty
- results changed
- Orange cannot open `.ows`

## Notes
Say:
Detailed analysis and conclusions are in the separate report.
