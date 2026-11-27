# Fed-Heart-Disease Initial Dataset Exploration

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

`01_dataset.ipynb` is purely exploratory. It does not modify, clean, or save any data.

**Steps in the notebook**

1. **Environment check:** prints the Python version, pandas version and NumPy version.
2. **Load the data:** builds a project-relative path, checks that all four files exist (and raises a clear error if not), then reads each file with `header=None` and `na_values="?"`, so the `?` placeholders are read as missing values (`NaN`).
3. **Name the columns:** assigns the 14 standard feature names (`age`, `sex`, `cp`, `trestbps`, `chol`, `fbs`, `restecg`, `thalach`, `exang`, `oldpeak`, `slope`, `ca`, `thal`, `num`) to every hospital.
4. **Inspect each hospital separately** (Cleveland, Hungary, Switzerland, Long Beach VA) with the same checks:
   - `head()`, `shape` and `index`
   - `info()` for data types and non-null counts
   - `describe()` for summary statistics
   - `isna().sum()` for missing values per column
   - `value_counts()` for every column
   - a check for columns that contain only zeros

**Shapes and missing values found** (each file has 14 columns)

| Hospital | Rows | Columns with the most missing values (`NaN`) |
|---|---|---|
| Cleveland | 303 | `ca` (4), `thal` (2); all other columns complete |
| Hungary | 294 | `ca` (291), `thal` (266), `slope` (190), `chol` (23), `fbs` (8) |
| Switzerland | 123 | `ca` (118), `fbs` (75), `thal` (52), `slope` (17), `oldpeak` (6) |
| Long Beach VA | 200 | `ca` (198), `thal` (166), `slope` (102), `trestbps` (56), `oldpeak` (56), `thalach` (53), `exang` (53) |

**Hidden missing values** (recorded as zeros instead of `?`)

- **Switzerland:** `chol` is `0` in all 123 rows. This is not a real measurement, so the column has no usable values. The all-zeros check flags only this column.
- **Long Beach VA:** `chol` has 49 zero values (in addition to 7 `NaN`), and `trestbps` has one zero. These are physiologically impossible and look like placeholders for missing values.

**Main observations**

- Cleveland is almost complete; the other three hospitals have large gaps.
- `ca`, `thal` and `slope` are missing for most patients in Hungary, Switzerland and Long Beach VA.
- Missingness differs by hospital, so any cleaning decision (for example, dropping columns or deciding whether to impute) has to be made with these differences in mind. No cleaning is applied in this notebook.

## References

- [FLamby repository](https://github.com/owkin/FLamby)
- [Fed-Heart-Disease dataset documentation and download instructions](https://github.com/owkin/FLamby/blob/main/flamby/datasets/fed_heart_disease/README.md)
