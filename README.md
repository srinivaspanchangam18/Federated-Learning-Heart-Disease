# Federated Learning on Heart Disease Dataset

## Project Overview

This project explores Federated Learning using the FLamby Fed-Heart-Disease dataset.

The dataset contains medical records distributed across four hospitals:

- Cleveland
- Hungary
- Switzerland
- Long Beach VA

The objective is to explore the dataset and prepare it for comparing Local Learning, Centralized Learning, and Federated Learning approaches.

## Repository Structure

```text
fed-heart-disease-project/
├── notebooks/
│   └── 01_dataset.ipynb
├── environment.yml
├── README.md
├── .gitignore
├── data/
│   └── heart_disease_dataset/
│       ├── processed.cleveland.data
│       ├── processed.hungarian.data
│       ├── processed.switzerland.data
│       └── processed.va.data
└── FLamby/
```
## Environment Setup

Create the Conda environment:

```bash
conda env create -f environment.yml
```

Activate it:

```bash
conda activate fed-heart-disease-eda
```

Launch Jupyter Notebook:

```bash
jupyter notebook
```

## Dataset Download Instructions

The dataset is obtained using the official FLamby repository.

1. Clone the FLamby repository:

```bash
git clone https://github.com/owkin/FLamby.git FLamby-main
```

2. Follow the official Fed-Heart-Disease dataset download instructions:

https://github.com/owkin/FLamby/tree/main/flamby/datasets/fed_heart_disease

3. Ensure the four hospital dataset files are available in the following directory:

```text
FLamby-main/flamby/datasets/fed_heart_disease/dataset_creation_scripts/heart_disease_dataset/
```

## Running the Notebook

1. Open `01_dataset.ipynb`.
2. Select the created Python environment as the kernel.
3. Run all cells from top to bottom.

## Dependencies

- Python
- Pandas
- NumPy
- Jupyter Notebook
- IPykernel

## References

- [FLamby GitHub Repository](https://github.com/owkin/FLamby)
- [Fed-Heart-Disease Dataset](https://github.com/owkin/FLamby/tree/main/flamby/datasets/fed_heart_disease)
