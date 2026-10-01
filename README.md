---
tags: [vision, medical, segmentation, fets, fds]
dataset: [FeTS 2022]
framework: [PyTorch, MONAI, NumPy & SciPy and Pandas]
---
# Federated 3D Brain Tumor Segmentation with FedIN-EDAR and Flower
This project implements **FedIN-EDAR**, an adaptive and communication-efficient federated learning framework for 3D multi-parametric brain tumor segmentation on the multi-institutional **FeTS 2022** benchmark using **Flower** and **PyTorch/MONAI**.
---

## Proposed Method Overview: FedIN-EDAR
**FedIN-EDAR** (**Fed**erated **I**nstance **N**ormalization with **E**MD and **D**ivergence-**A**ware **A**daptive **R**egularization) is a communication-frugal, domain-generalizable federated learning framework designed for multi-institutional 3D brain tumor segmentation under severe scanner and label heterogeneity (FeTS 2022).

### How It Works (4 Simple Steps):
1. **Local Normalization (FedIN):**
   - Keeps scanner-specific adjustments (Instance Normalization) local to each hospital. This stops scanner differences from confusing the shared global model.
2. **Tumor Size Profiling (EMD):**
   - In the first round, each hospital creates a simple profile (histogram) of its patient tumor sizes. The server uses Earth Mover's Distance (EMD) to measure how different each hospital's data is from the global average.
3. **Smart Hospital Regularization (EDAR):**
   - If a hospital has very unusual data, the system automatically applies a customized penalty (proximal term) to keep its training stable. This prevents outlier hospitals from pulling the shared model off track.
4. **Smart Scheduling (Saving Bandwidth):**
   - Instead of asking every hospital to send huge models in every round, it intelligently selects only a small subset of hospitals per round by using coolaborator selector. This allows the model to converge in just **21 rounds** while saving massive internet bandwidth.

### Key Results (FeTS 2022 Benchmark):
- **High Accuracy on New Hospitals:**
  - **Partitioning 1 (23 Hospitals):** Achieves **81.69%** Whole Tumor (WT), **74.18%** Tumor Core (TC), and **70.88%** Enhancing Tumor (ET) Dice score on global test set data from hospitals never seen during training.

  - **Partitioning 2 (33 Hospitals):** Achieves **73.58%** WT and **62.18%** TC Dice score on unseen test data.

- **Over 82% Bandwidth Savings:** Uses only **2.87 GB to 4.31 GB** of network data instead of the standard 17 GB to 24 GB required by traditional federated learning.

- **$4\times$ More Efficient:** Transmits nearly 4 times less data than the FeTS 2022 challenge 3rd-place benchmark (RegSimAgg).

- **Fast Convergence:** Reaches high performance in only **21 rounds**, compared to hundreds of rounds required by traditional methods.
---

## Environment Setup & Installation

Follow these simple steps to set up the project on your machine (tested on Windows PowerShell with Python 3.12.10):

### 1. Clone the Repository

```bash
git clone https://github.com/syedhaseeburrehman77-hash/FEDIN-EDAR-Brain-tumor-segmentation
```

### 2. Environment Setup

```powershell
# Allow script execution for the current session
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass

# Create virtual environment
python -m venv .venv

# Activate the virtual environment
.\.venv\Scripts\Activate.ps1

# Verify Python version
python --version
# Output: Python 3.12.10
```

### 3. Install Dependencies and Project

Install the local package in editable mode along with all required dependencies defined in `pyproject.toml` (PyTorch, MONAI, Flower, SciPy, etc.):

```powershell
pip install -e .
```

### Run Proposed Method (FedIN-EDAR)

Run our proposed FedIN-EDAR strategy on the full FeTS 2022 benchmark:

```powershell
# Default full execution
flwr run . --stream
```

To explicitly customize settings (e.g., 21 communication rounds on 33 institutions):

```powershell
flwr run . --stream --run-config 'algorithm="fedindar" num-server-rounds=21 num-clients=33 collaborator-selector="sliding"'
```
```

### Switching Between Partitioning 1 and Partitioning 2
Before Runing command to run, you can easily switch between partitioning file in `pyproject.toml` by changing path

- **Partitioning 1:** 23 Clinical Institutions (`num-clients = 23`— *Default*)
- **Partitioning 2:** 33 Clinical Institutions (`num-clients = 33`)

### Dataset
The following dataset is used in this repository.

[Brain tumor multimodal image (CT & MRI)](https://www.kaggle.com/datasets/murtozalikhon/brain-tumor-multimodal-image-ct-and-mri)

If you are already familiar with how the Deployment Engine works, you may want to learn how to run it using Docker. Check out the [Flower with Docker](https://flower.ai/docs/framework/docker/index.html) documentation.
