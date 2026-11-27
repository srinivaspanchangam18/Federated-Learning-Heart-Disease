# Fed-Heart-Disease — Initial Dataset Exploration

This repository contains the initial exploratory data analysis notebook for the FLamby Fed-Heart-Disease dataset.

The notebook reads the four hospital-level raw files (Cleveland, Hungary, Switzerland, and Long Beach VA). The dataset itself is **not committed** to this repository. Download it locally by following the steps below.

**Prerequisites:** [Conda](https://docs.conda.io/en/latest/miniconda.html) (Miniconda or Anaconda) and [Git](https://git-scm.com/downloads).

## 1. Project structure

After completing the setup, the relevant files/folders should look like this:

```text
fed-heart-disease-project/
├── 01_dataset.ipynb
├── environment.yml
├── README.md
├── .gitignore
├── data/
│   └── heart_disease_dataset/
│       ├── processed.cleveland.data
│       ├── processed.hungarian.data
│       ├── processed.switzerland.data
│       └── processed.va.data
└── FLamby/                 # cloned locally; not part of this analysis repo
```

Keep the dataset out of Git. The `.gitignore` file excludes the local data and the cloned FLamby checkout.

## 2. Create the Python environment

From the root of this repository, create the Conda environment:

```bash
conda env create -f environment.yml
```

Activate it:

```bash
conda activate fed-heart-disease-eda
```

Register it as a Jupyter kernel (optional, but useful when selecting the kernel in Jupyter):

```bash
python -m ipykernel install --user --name fed-heart-disease-eda --display-name "Python (fed-heart-disease-eda)"
```

## 3. Download the dataset

The data download procedure below follows the official [FLamby Fed-Heart-Disease instructions](https://github.com/owkin/FLamby/blob/main/flamby/datasets/fed_heart_disease/README.md).

With the environment activated, open a terminal in this repository's root folder and run:

```bash
git clone https://github.com/owkin/FLamby.git
```

Then move into the Heart Disease dataset download scripts folder:

```bash
cd FLamby/flamby/datasets/fed_heart_disease/dataset_creation_scripts
```

Run the official download script. This relative output path places the downloaded files in this repository's `data/heart_disease_dataset/` folder:

```bash
python download.py --output-folder ../../../../../data/heart_disease_dataset
```

When the download finishes, confirm that the `data/heart_disease_dataset/` folder contains these four files:

- `processed.cleveland.data`
- `processed.hungarian.data`
- `processed.switzerland.data`
- `processed.va.data`

**If you downloaded FLamby as a ZIP instead of cloning it:** the files end up in `FLamby-main/flamby/datasets/fed_heart_disease/dataset_creation_scripts/heart_disease_dataset/`. Copy the four files into `data/heart_disease_dataset/`, or set the `HEART_DISEASE_DATA_DIR` environment variable to that folder (see section 4).

**Dataset licence:** read and follow the licence and attribution requirements described in the official FLamby dataset README before using or redistributing the data.

## 4. Run the notebook

From the root of this repository, launch Jupyter:

```bash
jupyter lab
```

Open `01_dataset.ipynb` and select the **Python (fed-heart-disease-eda)** kernel. Then run every cell in order:

- In JupyterLab: **Kernel → Restart Kernel and Run All Cells**
- In classic Jupyter Notebook: **Kernel → Restart & Run All**

Run Jupyter from the repository root where possible. The notebook first checks `data/heart_disease_dataset/`. If that folder is not present and you already downloaded the files inside `FLamby-main/`, it also checks the existing `FLamby-main/flamby/datasets/fed_heart_disease/dataset_creation_scripts/heart_disease_dataset/` folder automatically. An explicitly set `HEART_DISEASE_DATA_DIR` takes priority.

If your data is stored in a different location (for example, the `FLamby-main/.../heart_disease_dataset/` folder created when you downloaded FLamby as a ZIP), set `HEART_DISEASE_DATA_DIR` to the folder containing the four `.data` files **before launching Jupyter**.

On Windows PowerShell:

```powershell
$env:HEART_DISEASE_DATA_DIR = "C:\path\to\heart_disease_dataset"
jupyter lab
```

On macOS/Linux:

```bash
export HEART_DISEASE_DATA_DIR="/path/to/heart_disease_dataset"
jupyter lab
```

The notebook uses this override when it is set. If it is not set, it looks for `data/heart_disease_dataset/` under the repository root. Relative override paths are interpreted relative to the repository root.

## 5. Dependencies

The `environment.yml` file contains the packages required by this notebook and the Jupyter kernel: Python 3.10, pandas 2.3, NumPy 1.26, JupyterLab, ipykernel and wget. It is a lightweight analysis environment; it is separate from FLamby's own full development environment.

## 6. What the notebook does

`01_dataset.ipynb` performs initial exploratory data analysis (EDA) on the four hospital datasets. It does not clean, impute, or save the source data. The binary target is created in memory for analysis.

### Notebook workflow

1. **Check the environment:** prints the Python, pandas and NumPy versions.
2. **Locate the dataset:** uses `HEART_DISEASE_DATA_DIR` if set; otherwise checks `data/heart_disease_dataset/` and the existing `FLamby-main/.../heart_disease_dataset/` location.
3. **Load the files:** reads the four `.data` files with `header=None` and converts `?` placeholders to `NaN`.
4. **Name the columns:** assigns the 14 feature names: `age`, `sex`, `cp`, `trestbps`, `chol`, `fbs`, `restecg`, `thalach`, `exang`, `oldpeak`, `slope`, `ca`, `thal`, and `num`.
5. **Explore each hospital separately:** inspects the first rows, shape, index, data types, non-null counts, descriptive statistics, missing values and categorical value counts.
6. **Check zero values:** examines zero counts in selected columns (`age`, `trestbps`, `chol`, `thalach`, `thal`) and checks for columns containing only zeros.
7. **Create a binary target:** adds `target = (num > 0).astype(int)` to each hospital DataFrame. Class 0 means `num = 0`; class 1 means `num > 0`. The original `num` column is retained.
8. **Summarize data quality and target balance:** reports each column's missing percentage, zero count and zero percentage, plus target counts, percentages and the majority-to-minority class ratio.

### Dataset overview

Each hospital file has 14 original columns.

| Hospital | Rows | Main missing-value columns |
|---|---:|---|
| Cleveland | 303 | `ca` (4), `thal` (2) |
| Hungary | 294 | `ca` (291), `thal` (266), `slope` (190), `chol` (23), `fbs` (8) |
| Switzerland | 123 | `ca` (118), `fbs` (75), `thal` (52), `slope` (17), `oldpeak` (6) |
| Long Beach VA | 200 | `ca` (198), `thal` (166), `slope` (102), `trestbps` (56), `oldpeak` (56), `thalach` (53), `exang` (53) |

### Main findings

- **Cleveland:** has relatively few missing values. `ca` and `thal` contain 4 and 2 missing values, respectively. Its binary target is relatively balanced (class 0: 54.1%; class 1: 45.9%).
- **Hungary:** has extensive missing data in `ca`, `thal` and `slope`. The target distribution is class 0: 63.9% and class 1: 36.1% (imbalance ratio 1.77:1).
- **Switzerland:** `chol` is zero in all 123 records, so it has no usable cholesterol measurements. `ca` has 95.9% missing values. The target is highly imbalanced (class 0: 6.5%; class 1: 93.5%).
- **Long Beach VA:** has extensive missing data, especially in `ca` (99%), `thal` (83%) and `slope` (51%). `chol` contains 49 zero values and `trestbps` contains one zero, which may represent unavailable measurements. The target distribution is class 0: 25.5% and class 1: 74.5% (imbalance ratio 2.92:1).

Missingness and zero-value patterns differ across hospitals. These findings should guide later preprocessing and modeling; this notebook documents the data and does not apply those cleaning decisions.

## References

- [FLamby repository](https://github.com/owkin/FLamby)
- [Fed-Heart-Disease dataset documentation and download instructions](https://github.com/owkin/FLamby/blob/main/flamby/datasets/fed_heart_disease/README.md)
