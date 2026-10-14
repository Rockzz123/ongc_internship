# ROP Prediction & Drilling Parameter Optimization

A Python-based machine-learning project for predicting **Rate of Penetration (ROP)** and exploring drilling-parameter recommendations using ONGC Well 1 drilling data. The project was developed during an internship with ONGC Rajahmundry Asset.

> **Important:** This repository is a research/academic prototype, not an operational drilling-control system. Any parameter recommendations must be independently reviewed by qualified drilling engineers and validated against field, equipment, well-control, and site-specific constraints before use.

## Project at a glance

- **Problem:** ROP varies with depth, lithology, bit operating conditions, and drilling phase. A single model trained on mixed phases can struggle when the phases have different operating regimes.
- **Approach:** Clean and aggregate sensor data, harmonize lithology encoding, engineer drilling-mechanics and depth-history features, train gradient-boosted tree models, and explore constrained parameter optimization.
- **Models compared:** Artificial Neural Network (ANN), XGBoost, and LightGBM.
- **Tuning:** Optuna / Tree-structured Parzen Estimator (TPE) hyperparameter search.
- **Optimization concept:** Search candidate Weight on Bit (WOB), rotary speed (RPM), and flow-rate settings while constraining changes around a baseline. The report describes a 10×10×10 candidate grid and a ±10% guardrail.

## Reported results

The internship report records the following combined-data results for the upgraded LightGBM pipeline:

| Model / configuration | R² | MAE (m/hr) | RMSE (m/hr) |
|---|---:|---:|---:|
| ANN baseline | ~0.32 | ~9.1 | ~17.5 |
| XGBoost baseline | ~0.36 | ~7.8 | ~16.1 |
| LightGBM baseline | 0.3680 | 7.30 | 15.23 |
| Upgraded LightGBM | **0.5684** | **4.36** | **8.45** |

These values are taken from the accompanying report and should be treated as reported experimental results, not as a guarantee of performance on other wells. Per-phase models are also discussed in the report and have different scores from the combined model.

## Repository structure

```text
rop-prediction-drilling-optimization/
├── README.md
├── requirements.txt
├── .gitignore
├── notebooks/
│   └── ongc_well1_upgraded.ipynb
├── reports/
│   └── ONGC_ROP_Prediction_Internship_Report.pdf
├── data/
│   └── README.md
└── outputs/
    └── README.md
```

## Workflow

1. **Load data:** Read the processed drilling datasets for the relevant well phases.
2. **Clean records:** Filter non-drilling activity and implausible measurements; apply outlier handling and depth-based aggregation where applicable.
3. **Harmonize lithology:** Use one consistent lithology-to-code mapping across datasets.
4. **Engineer features:** Combine depth, WOB, RPM, torque, flow/pressure information, interaction terms, and historical rolling features.
5. **Train and evaluate:** Compare ANN, XGBoost, and LightGBM using R², MAE, and RMSE.
6. **Tune LightGBM:** Use Optuna to search hyperparameters.
7. **Explore optimization:** Evaluate candidate drilling settings using model predictions and stated operational guardrails.
8. **Review visualizations and outputs:** Inspect prediction errors, feature importance, depth trends, and candidate parameter changes.

## Getting started

### Option A — Google Colab

The notebook includes Google Colab-oriented upload/download steps. Upload the notebook to Colab, then upload the processed CSV files expected by the notebook. Run cells in order and check each cell's input filenames before execution.

### Option B — Local Python environment

1. Install Python 3.10 or a compatible version.
2. Create and activate a virtual environment.
3. Install dependencies:

   ```bash
   pip install -r requirements.txt
   ```

4. Place the required processed CSV files in the project root (or update the notebook's file paths).
5. Open the notebook:

   ```bash
   jupyter lab
   ```

**Data files are not included in this repository package.** The notebook references processed CSVs such as `17_5_phase_Processed_v3.csv`, `Well-1_Processed_with_ROP.csv`, and files used by later multi-well experiments. The exact files required depend on which notebook section you run. Obtain permission to share/use the source data, and keep confidential or operational data out of a public repository unless you are authorized to publish it.

## Inputs and feature concepts

The report describes drilling measurements such as:

- `Depth_m` — bit depth in metres
- `WOB_klbm` — weight on bit
- `RPM` — rotary speed
- `FlowRate_gpm` — mud flow rate
- `Torque_ftlbf` — surface torque
- pressure / hydraulic proxy features
- lithology class
- derived interaction and history features, including WOB × RPM, torque per WOB, rolling means, deltas, and rolling variability

Column names must match those expected by the selected notebook section. Review the data-loading and feature-selection cells before running the workflow on a different dataset.

## Limitations

- The reported evaluation is based on the datasets and split used in the project; performance can change on unseen wells or different drilling conditions.
- Lithology labels and feature columns must be mapped consistently between datasets.
- Rolling features must use only past observations; using current/future target values would cause data leakage.
- The notebook contains Colab-specific commands and multiple experimental sections. Some cells may require adaptation for local execution, and not every cell is necessarily part of one single end-to-end run.
- The optimization logic is a simulation aid. Its simplified model and constraints do not replace hydraulic, well-control, equipment, geomechanical, or human engineering review.

## Suggested future improvements

- Refactor notebook sections into reusable Python modules and a single reproducible pipeline.
- Add a data dictionary and schema/validation checks for all input CSVs.
- Use well-wise or time/depth-aware holdout validation to test generalization and prevent leakage.
- Add model explainability (e.g., SHAP), uncertainty estimates, and calibration/error analysis by lithology.
- Add tests for feature engineering, lithology mapping, and optimizer constraints.
- Build a small Streamlit dashboard only after the prediction pipeline is reproducible and validated.

## Acknowledgment

This work was carried out as an internship project associated with ONGC Rajahmundry Asset. See the report in `reports/` for the project context, methodology, and results.
