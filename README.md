# Relational Datasets EDA

Exploratory data analysis notebooks and reusable Python utilities for profiling relational datasets. The project currently includes worked analyses for:

- **Berka banking dataset** from Kaggle
- **rel-hm dataset** from RelBench

Both notebooks build a single analysis-ready table from source relational tables, then run a shared set of EDA helpers for profiling, distribution analysis, correlation exploration, outlier detection, and missing data analysis.

This project is inspired by the KDnuggets article [5 Useful Python Scripts to Automate Exploratory Data Analysis](https://www.kdnuggets.com/5-useful-python-scripts-to-automate-exploratory-data-analysis). The Python utilities for exploratory data analysis are taken from the [`useful-python-scripts-eda`](https://github.com/balapriyac/data-science-tutorials/tree/main/useful-python-scripts-eda) directory in the `balapriyac/data-science-tutorials` repository.

## Project Structure

```text
.
├── berka-dataset/
│   └── berka_dataset_eda.ipynb
├── rel-hm/
│   └── rel_hm_eda.ipynb
├── useful-python-scripts-eda/
│   ├── correlation_explorer.py
│   ├── data_profiler.py
│   ├── distribution_analyzer.py
│   ├── missing_data_analyzer.py
│   ├── outlier_suite.py
│   └── README.md
└── requirements.txt
```

## Installation

1. Clone the repository and enter the project directory.

```bash
git clone <repo-url>
cd rel_datasets_eda
```

2. Create and activate a virtual environment.

```bash
python3 -m venv .venv
source .venv/bin/activate
```

3. Install the Python dependencies.

```bash
pip install -r requirements.txt
```

4. Configure dataset access as needed.

For the Berka notebook, install a Kaggle API token at `~/.kaggle/kaggle.json`. The notebook downloads `marceloventura/the-berka-dataset`.

For the rel-hm notebook, RelBench downloads the dataset through `relbench.datasets.get_dataset`.

## Usage

Start Jupyter from the project root:

```bash
jupyter notebook
```

Then open one of the included notebooks:

- `berka-dataset/berka_dataset_eda.ipynb`
- `rel-hm/rel_hm_eda.ipynb`

Each notebook imports the reusable EDA utilities from `useful-python-scripts-eda`, loads a relational dataset, creates a joined analysis table, and runs the same analysis workflow.

## Usage Examples

### Run the Data Profiler

```python
import sys
import os
import pandas as pd

sys.path.append(os.path.abspath("useful-python-scripts-eda"))

from data_profiler import DataProfiler

df = pd.read_csv("data.csv")
profiler = DataProfiler(df, high_cardinality_threshold=0.5)

profiler.print_summary()

report = profiler.generate_full_profile()
print(report["overview"])
print(report["numeric_profiles"])
print(report["categorical_profiles"])
print(report["data_quality_issues"])
```

### Analyze Distributions

```python
from distribution_analyzer import DistributionAnalyzer

analyzer = DistributionAnalyzer(df)
distribution_report = analyzer.generate_distribution_report()

print(distribution_report)

analyzer.plot_numeric_distributions(max_cols=10)
analyzer.plot_boxplots()
analyzer.plot_qq_plots()
analyzer.plot_categorical_distributions()
```

### Explore Correlations

```python
from correlation_explorer import CorrelationExplorer

explorer = CorrelationExplorer(df)

high_corr = explorer.find_high_correlations(threshold=0.7, method="pearson")
print(high_corr)

vif = explorer.calculate_vif()
print(vif[vif["vif"] > 10])

explorer.plot_correlation_heatmap(method="pearson")
explorer.plot_top_correlations(n_pairs=10)
```

### Detect Outliers

```python
from outlier_suite import OutlierSuite

suite = OutlierSuite(df)

summary = suite.compare_methods_all_columns()
print(summary)

suite.plot_outlier_comparison("amount")

outliers = suite.detect_isolation_forest_outliers(contamination=0.1)
print(f"Found {outliers.sum()} multivariate outliers")
```

### Analyze Missing Data

```python
from missing_data_analyzer import MissingDataAnalyzer

analyzer = MissingDataAnalyzer(df)
report = analyzer.generate_full_report()

print(report["summary"])
print(report["patterns"])
print(report["classifications"])
print(report["recommendations"])

analyzer.plot_missing_bar()
analyzer.plot_missing_heatmap(max_cols=30)
analyzer.plot_missing_correlation()
```

## Notes

- The Berka notebook creates an `input/berka-dataset` directory when downloading Kaggle files.
- Some notebook cells may take time because they download datasets or generate multiple visualizations.
- More detailed examples for each reusable helper are available in `useful-python-scripts-eda/README.md`.
