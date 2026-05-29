# PULSE Demo

This directory is the packaged demo workspace for PULSE across QML, DFWM, RL, and the migrated super-sampling path.

The one-command demo entry for the whole package is:

```bash
cd /root/autodl-tmp/Demo/Demo
python run_demo.py --task qml_figures
```

Important data availability note:
- Only the QML validation demo is publicly runnable with bundled data.
- QML training, DFWM, RL, and Super-sampling code paths are included and documented, but their training datasets are restricted and are therefore not redistributed in this package.
- `run_demo.py` is the canonical one-command runner for all packaged tasks.

## 1. System Requirements

### Tested environment
- Operating system: Linux 5.15 or newer
- Python: 3.12.3
- PyTorch: 2.8.0+cu128
- CUDA runtime used by PyTorch: 12.8
- cuDNN: 91002

### Core Python dependencies
- `torch==2.8.0`
- `numpy==2.3.2`
- `scipy==1.17.1`
- `pandas==3.0.2`
- `matplotlib==3.10.5`
- `PyYAML==6.0.2`
- `segmentation-models-pytorch==0.5.0`
- `gymnasium==1.2.3`
- `stable-baselines3==2.8.0`
- `neuraloperator`
- `Pillow`

### Bundled local dependencies
- `QML/complexPytorch`
- `DFWM/complexPytorch`
- `RL/complexPytorch`
- `Super-sampling/snoo_qml_portable/_vendor/complexPytorch`

### Hardware recommendation
- Public QML validation demo: CPU is sufficient, recommend at least 8 GB RAM
- QML or DFWM training: NVIDIA GPU recommended, at least 16 GB VRAM
- RL surrogate and agent training: NVIDIA GPU recommended, at least 24 GB VRAM
- Super-sampling PULSE or baseline training: NVIDIA GPU recommended, at least 16 GB VRAM

## 2. Installation Guide

### Conda installation
```bash
cd /root/autodl-tmp/Demo/Demo
conda env create -f environment.yml
conda activate pulse-demo
```

### Pip installation
```bash
cd /root/autodl-tmp/Demo/Demo
python -m venv .venv
source .venv/bin/activate
pip install --upgrade pip
pip install -r requirements.txt
```

### Estimated installation time
- Desktop workstation: 10 to 20 minutes
- GPU server with cached wheels: 5 to 15 minutes
- CPU-only environment: 5 to 10 minutes, but training and plotting steps may be slower

## 3. Demo Instructions

### QML public validation demo
This is the only fully public, bundled-data validation task in the package.

#### One-command run
```bash
cd /root/autodl-tmp/Demo/Demo
python run_demo.py --task qml_figures
```

#### Input data
- Public validation data: `QML/Dataset/qml_val_power12.npy`

#### Main script
- `QML/reproduce_requested_figures.py`

#### Output files
- `QML/outputs/validation_visualizations/Plot4Paprer/plot4paper_wavelength_mae_8models.png`
- `QML/outputs/validation_visualizations/Plot4Paprer/plot4paper_wavelength_mae_8models.csv`
- `QML/outputs/validation_visualizations/Plot4Paprer/plot4paper_wavelength_mae_summary_8models.json`
- `QML/outputs/final_v3_continuous_period_export/flattened_preview.png`
- `QML/outputs/final_v3_continuous_period_export/heatmap_preview.png`
- `QML/outputs/final_v3_continuous_period_export/selection_summary.json`

#### Expected runtime
- CPU: about 1 to 3 minutes
- GPU: usually under 1 minute

#### Expected results
- Regenerates public visualization figures
- Writes wavelength-wise MAE statistics in CSV and JSON
- Exports continuous-period flattened curves and truth/prediction heatmaps
- Produces quantitative public validation outputs centered on MAE and visualization artifacts

### QML training task
This task is packaged but requires restricted training data.

#### Run
```bash
cd /root/autodl-tmp/Demo/Demo
python run_demo.py --task qml_train
```

#### Required data
- `QML/Dataset/qml_train_test_21powers.npy`

#### Main script
- `QML/run_final_v3_single.py`

#### Output location
- `QML/outputs/final_v3/single_run/`

#### Expected results
- Writes `stage1/`, `stage2/`, `training_summary.json`, `summary.csv`, `aggregated_summary.json`
- Reports training metrics such as `train_l2`, `test_l2`, and `val_l2`

### DFWM PULSE task
This task is packaged but cannot be publicly validated here because the DFWM training dataset is not redistributed.

#### Run
```bash
cd /root/autodl-tmp/Demo/Demo
python run_demo.py --task dfwm_train
```

#### Required data
- `DFWM/Dataset/mcs_xy_pairs_w128.npz`

#### Main scripts
- Canonical merged entry: `DFWM/trian_pulse.py`
- Compatibility entry: `DFWM/train_pulse_final_v3.py`
- Compatibility module alias: `DFWM/model.py`

#### Output location
- `DFWM/outputs/final_v3/`

#### Expected results
- Writes stage logs, model weights, grouped metrics, and `training_summary.json`
- Stage 1 reconstructs observation features
- Stage 2 trains `model_in + decoder + segmentation_head`
- Metrics include reconstruction or regression losses such as `train_l2`, `test_l2`, `val_l2`, and grouped `LpLoss`

### RL PULSE task
This task is packaged but requires restricted surrogate-training data.

#### Run
```bash
cd /root/autodl-tmp/Demo/Demo
python run_demo.py --task rl_train
```

#### Required data
- `RL/data_expanded/`
- Optional stronger-target export data: `RL/data_hybrid_strong/`

#### Main scripts
- `RL/env/train_pulse.py`
- `RL/utils/sequence_baseline_benchmark.py`
- `RL/rl/train_agents.py`
- `RL/utils/evaluation.py`

#### Output locations
- `RL/results/`
- `RL/results/baseline_benchmark/`
- `RL/results/Plot4Paper/`

#### Expected results
- Writes surrogate checkpoints, baseline benchmark results, RL logs, evaluation JSON or CSV summaries, plots, and lock-state monitoring traces

### Super-sampling migrated package
The migrated super-sampling code from `SNOO-Trad` is integrated under `Super-sampling/`. In this packaged demo, the super-sampling SNOO path is referred to as PULSE.

#### Selected baseline CV task
```bash
cd /root/autodl-tmp/Demo/Demo
python run_demo.py --task supersampling_selected_baselines
```
- Main script: `Super-sampling/train_selected_baselines.py`
- Models: `Unet`, `RRDB`, `NafNet`, `MambaIR`
- Default outputs: `Super-sampling/outputs/selected_baselines/cv5_fixed_val/`
- Uses real data when `--raw-data` exists; otherwise can fall back to deterministic synthetic data for validation-only checks

#### QML plus RRDB baseline task
```bash
cd /root/autodl-tmp/Demo/Demo
python run_demo.py --task supersampling_rrdb
```
- Main script: `Super-sampling/train_other_rrbd_supersampling.py`
- Models: `UNO-big`, `FNO-big`, `SKUNet`, `Mo-s2`, `OpticalNO`, `PredRNN`, `PhyDNet`, `LNO`
- Default outputs: `Super-sampling/outputs/qml_rrdb_baselines/`

#### PULSE super-sampling task
```bash
cd /root/autodl-tmp/Demo/Demo
python run_demo.py --task pulse_supersampling
```
- Main script: `Super-sampling/train-pulse.py`
- Keeps the original two-stage training logic and final_v3 `model_in + model_G` structure
- Default outputs: `Super-sampling/outputs/supersampling_finv3_snoo/`
- Expected metrics and logs include training losses, validation losses, and saved checkpoints

#### Migration validation task
```bash
cd /root/autodl-tmp/Demo/Demo
python run_demo.py --task supersampling_validate
```
- Main script: `Super-sampling/validate_migration.py`
- Runs constructor, forward, and one-step training validation for the migrated baselines and PULSE path
- Writes `Super-sampling/outputs/validation_artifacts/migration_validation_report.json`

## 4. Instructions For Use

### Replace with your own data
- QML public validation inference: replace `QML/Dataset/qml_val_power12.npy`
- QML training: add `QML/Dataset/qml_train_test_21powers.npy`
- DFWM training: add `DFWM/Dataset/mcs_xy_pairs_w128.npz`
- RL training: place surrogate-training data under `RL/data_expanded/`
- RL stronger-target export workflows: optionally place data under `RL/data_hybrid_strong/`
- Super-sampling real training: pass or place your actual raw dataset at the path required by the corresponding `Super-sampling` script

### Run different tasks through the one-command runner
- Public QML figures: `python run_demo.py --task qml_figures`
- QML PULSE training: `python run_demo.py --task qml_train`
- DFWM PULSE training: `python run_demo.py --task dfwm_train`
- RL PULSE training: `python run_demo.py --task rl_train`
- Migrated selected baselines: `python run_demo.py --task supersampling_selected_baselines`
- Migrated QML plus RRDB baselines: `python run_demo.py --task supersampling_rrdb`
- Migrated PULSE super-sampling: `python run_demo.py --task pulse_supersampling`
- Migrated package validation: `python run_demo.py --task supersampling_validate`

### Run task scripts directly
- QML public validation: `python QML/reproduce_requested_figures.py`
- QML training: `python QML/run_final_v3_single.py`
- DFWM PULSE training: `python DFWM/trian_pulse.py`
- RL surrogate training: `python RL/env/train_pulse.py`
- RL baseline benchmark: `python RL/utils/sequence_baseline_benchmark.py`
- RL agent training: `python RL/rl/train_agents.py`
- Super-sampling PULSE training: `python Super-sampling/train-pulse.py`

### Custom output locations
- QML single-run training supports `--output-root`
- DFWM, RL, and Super-sampling tasks can be adjusted through their local training scripts and config files

## 5. Package Overview

### QML package
- Main public validation entry: `QML/reproduce_requested_figures.py`
- Main training entry: `QML/run_final_v3_single.py`
- Public validation data: `QML/Dataset/qml_val_power12.npy`
- Main outputs: `QML/outputs/final_v3/`, `QML/outputs/validation_visualizations/Plot4Paprer/`, `QML/outputs/final_v3_continuous_period_export/`

### DFWM package
- Unified training entry: `DFWM/trian_pulse.py`
- Compatibility entries: `DFWM/train_pulse_final_v3.py`, `DFWM/train_snoo_final_v3.py`
- Unified model module: `DFWM/PULSE.py`
- Compatibility model alias: `DFWM/model.py`
- Baseline script: `DFWM/train_mcs_all.py`

### RL package
- Surrogate and environment code: `RL/env/`
- Benchmark and evaluation utilities: `RL/utils/`
- Agent training: `RL/rl/`
- Main configs: `RL/configs/`

### Super-sampling package
- Baseline CV entry: `Super-sampling/train_selected_baselines.py`
- RRDB baseline entry: `Super-sampling/train_other_rrbd_supersampling.py`
- PULSE training entry: `Super-sampling/train-pulse.py`
- Shared data loader and optional synthetic fallback: `Super-sampling/data.py`
- Validation script: `Super-sampling/validate_migration.py`

## 6. License

- License notice file: `LICENSE`
- This packaged demo does not redistribute restricted datasets
- No new patent or trademark license is granted by this packaging layer
- Verify the licenses of upstream third-party libraries before external redistribution

## 7. Code Function Map And Pseudocode

### PULSE `model_G`
- `QML/model.py::build_model_g()`
- `DFWM/PULSE.py::build_model_g()`
- `RL/env/snoo_model.py::build_pulse_model_g`
- `Super-sampling/PULSE.py::build_supersampling_finv3_snoo()` constructs the migrated super-sampling PULSE path around `model_G`

### PULSE `model_in`
- `QML/model.py::PyramidOperator`
- `DFWM/PULSE.py::PyramidOperator`
- `RL/env/snoo_model.py::PyramidOperator`
- `Super-sampling/snoo_qml_portable/model.py::PyramidOperator`

### PULSE `ECNNHead`
- `QML/model.py::ECNNHead`
- `DFWM/PULSE.py::ECNNHead`
- `RL/env/snoo_model.py::ECNNHead`
- `Super-sampling/PULSE.py::ECNNHead`

### Baseline models
- `QML/base-model/models.py`
- `DFWM/base-model/models.py`
- `RL/env/sequence_baseline_models.py`
- `Super-sampling/other_rrbd_supersampling.py`
- `Super-sampling/train_selected_baselines.py`

### Evaluation metrics
- `QML/outputs/validation_visualizations/Plot4Paprer/generate_plot4paper_wavelength_mae_data.py`
- `QML/final_v3_continuous_period_export/export_best_continuous_period_data.py`
- `RL/utils/evaluation.py`
- `RL/utils/sequence_baseline_benchmark.py`
- `Super-sampling/metrics.py`

### Code function map and pseudocode documents
- `docs/CODE_MAP.md`
- `docs/PSEUDOCODE.md`
