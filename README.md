# DATA_OVERVIEW.md

[![Agent Collab Treaty](https://raw.githubusercontent.com/yzhaoinuw/agent_collab_treaty/main/assets/treaty-adopted.svg)](https://github.com/yzhaoinuw/agent_collab_treaty)

Natural-language index of `/gpfs/fs3/archive/dkell12_lab/BrainFlowZZZ/`.  
This is a shared archive for the Nedergaard lab's sleep-neuroscience project (rodent EEG/EMG, LFP, fiber photometry, two-photon imaging, CSD). Use this file to quickly locate data or code before digging into subfolders.

---

<!-- BEGIN:data-at-a-glance -->
## Data at a glance

| Data type | Count | Folders |
|---|---|---|
| EEG/EMG recordings (`.exp` files) | 92 | `eeg1/`, `eeg2/`, `EEG_NE/`, `EEG_NE_ASCII/`, `data_eeg_emg_241114/`, `data_eeg_emg_ne_241114/` |
| Two-photon TIF image stacks | 57 | `CSD-TIF-Files/`, `CSD_Lymphatic_longBL/`, `CSD_Lymphatic_longBL_vs2/`, `VesselDiameterVerena/` |

---
<!-- END:data-at-a-glance -->

<!-- BEGIN:quick-lookup -->
## Quick lookup by topic

| I'm looking for… | Go to |
|---|---|
| Raw EEG/EMG sleep recordings | `eeg1/`, `eeg2/` |
| EEG/EMG + norepinephrine (NE) recordings | `EEG_NE/`, `EEG_NE_ASCII/`, `data_eeg_emg_241114/`, `data_eeg_emg_ne_241114/` |
| MATLAB tools to read `.exp` / `.bin` / `.xls` recording files | `EEGtoolbox/` |
| Automated sleep staging (model training) | `sleep_scoring_model_training/` |
| Automated sleep staging (inference/deployment) | `Yue/sleep_scoring/` |
| New data uploaded to test/extend the sleep model (2023) | `20231006_new_data_for_testing_the_model/` |
| Converting any recording to NWB format | `make_nwb/` |
| CSD (cortical spreading depression) TIF images | `CSD-TIF-Files/`, `CSD_Lymphatic_longBL/`, `CSD_Lymphatic_longBL_vs2/` |
| Vessel / artery diameter analysis from TIF | `VesselDiameterVerena/` |
| Astrocyte endfoot geometry & AQP4/MLC1 imaging | `EndfootGeometry/` |
| Viral tool core files / Alphafold predictions | `Viral_Tool_Core/` |

---
<!-- END:quick-lookup -->

## Folder-by-folder descriptions

<!-- BEGIN:eeg-emg -->
### EEG & EMG raw recordings

**`eeg1/`** *(5.5 GB, 11 sessions)*  
Sleep-scored EEG + EMG recordings from CTN Copenhagen. Each subfolder is named `<animalID>_<timeBin>s` (e.g., `237_1s` = animal 237, 1-second sleep-scoring bins). Each subfolder contains the full set of Viewpoint files: `.exp` (raw EEG/EMG), `.bin`, `.csv`, `.H` (hypnogram), `.act` (actimetry, unused), `.xls` (sleep scores with "quantitatif" in name), and `.avi` (video). See `readme.txt` in root for file format details and a sample MATLAB analysis script.

**`eeg2/`** *(4.9 GB, 7 sessions)*  
Same format as `eeg1/` but recordings also include LFP (local field potential) stored as a third channel (EEG3) in the `.exp` file. Subfolders are named `LFP<animalID>`.

**`EEG_NE/`** *(22 GB, 27 sessions)*  
EEG/EMG recordings that include norepinephrine (NE) fiber photometry. Same Viewpoint file structure as eeg1/eeg2.

**`EEG_NE_ASCII/`** *(6.0 GB, 5 sessions)*  
ASCII-exported version of the `EEG_NE/` data.

**`data_eeg_emg_241114/`** *(16 GB)*  
EEG + EMG recordings collected with NE photometry, uploaded November 2024. Contains both session-named folders (e.g., `20210712_EEGFP_1_477_2_486_sal`) and per-animal folders (`477_sal`, `484_sal`, …). Subfolders for experimenters Julia and Klaudia also present.

**`data_eeg_emg_ne_241114/`** *(101 GB)*  
NE-scored EEG/EMG data, also uploaded November 2024. Subfolders: `Anastasia_scored_data/`, `Klaudia/`.

---
<!-- END:eeg-emg -->

<!-- BEGIN:code-toolboxes -->
### Code & toolboxes

**`EEGtoolbox/`** *(1.7 MB)*  
MATLAB toolbox (originally from CTN Copenhagen) for processing Viewpoint `.exp`/`.bin` recordings. Key functions:
- `loadEXP.m` — load a Viewpoint `.exp` file into MATLAB
- `ExtractContinuousData.m` — extract EEG/EMG time series
- `ExtractFullHypno.m`, `ChangeState.m` — hypnogram/sleep stage utilities
- `Bin2EDF.m` / `edfread.m` — convert to/from EDF format
- `MeanPowerSpectrumPerGroup.m` — power spectrum analysis
- `ReadBinFile.m`, `WriteBinFile.m` — low-level binary I/O
- `StateQuantification.m` — quantify time in each sleep stage  
Add this folder to the MATLAB path before working with any Viewpoint files.

**`make_nwb/`** *(61 MB)*  
MATLAB scripts for converting lab recordings to NWB (Neurodata Without Borders) format. Contains person-specific conversion scripts (`felix2nwb.m`, `marta12nwb.m`, `michael2nwb.m`, `kim2nwb.m`, `keelin2nwb_raw.m`, `tiffs2nwb.m`) and utilities (`read_abf.m`, `readExperiment.m`, `read_tiff.m`, `read_nwb.m`, `write_nwb.m`). Depends on the `matnwb/` and `npy-matlab/` libraries included in the folder.

**`Yue/sleep_scoring/`** *(5.7 MB)*  
Python package for automated sleep stage classification (inference/deployment). Built on the sDREAMER model trained in `sleep_scoring_model_training/`. Contains:
- `app_src/` — application source code
- `models/` — trained model weights and configurations
- `tests/` — unit tests
- `packaging/` — Python package configuration
- `work_log_archive/` — documentation, notes, and development logs
This is the inference tool for applying sleep scoring to EEG + EMG recordings; see `sleep_scoring_model_training/` for the model training pipeline.

**`sleep_scoring_model_training/`** *(21 GB)*  
Machine-learning pipeline for automated sleep staging (Wake / NREM / REM) from EEG + EMG. Implements **sDREAMER** (Seq-HMoE: Sequence-level Hierarchical Mixture-of-Experts transformer, published IEEE ICDH 2023).
- `sdreamer_flow/` — main model code: `run_train_sdreamer.py` (training entry point), `write_data_sdreamer.py` (data prep), `models/`, `data_provider/`, `exp/` (experiment runners), `environment.yml`
- `sdreamer_input_data/` — raw input data for training
- `sdreamer_output_data/` / `sdreamer_output_data_augmented/` — preprocessed/augmented training sets
- `sdreamer_checkpoints/` — saved model weights
- `preprocessing.py`, `make_augmented_data.py` — data preprocessing utilities
- `HMoE_training_log.txt`, `sdreamer_training_log.txt` — training run logs  
Model classifies 3 sleep states using EEG + EMG (+ optionally NE) channels.

**`VesselDiameterVerena/`** *(128 GB)*  
MATLAB scripts for measuring cerebral artery diameter from two-photon `.tif` files:
- `find_diameter_skc.m` — reads a `.tif` stack and extracts artery diameter trace
- `analysis_skc.m` — detects bad indices, corrects them, and isolates segments of interest  
Per-animal recording folders: `20230328_VU72_SwiChR/`, `20230421_VU73_NpHR/`, `20230720_VU78_SwiChR_IOS/`.

**`20231006_new_data_for_testing_the_model/`** *(110 GB)*  
A mixed folder of newer experimental data and preprocessing code added in 2023 for extending/testing the sleep model. Contains:
- `preprocess_sleep_data/` — MATLAB scripts (`preprocess_sleep_data.m`, `Data_core_NE_EEG.m`, `LoadFPandEEG_Klaudia.m`, `plot_sleep.m`) + data subfolders by condition (opto_ChR2, opto_Arch, saline_injection, AudiStim, etc.)
- `Mie_newdata/` — new recordings from experimenter Mie
- `Anastasia/`, `Tessa_TDTdata/` — recordings from Anastasia and Tessa (TDT system)
- `list_content.py` — utility to list directory contents

---
<!-- END:code-toolboxes -->

<!-- BEGIN:imaging -->
### Imaging data

**`CSD-TIF-Files/`** *(177 GB)*  
Two-photon `.tif` image stacks for CSD (cortical spreading depression) experiments across ~14 mice (`M1`–`M14`). Each mouse has a `_Baseline.tif` and a `_Kcl.tif` (KCl-induced CSD).

**`CSD_Lymphatic_longBL/`** *(38 GB)*  
CSD + lymphatic imaging data with extended baseline, for mice M16 and M20. `.tif` files only.

**`CSD_Lymphatic_longBL_vs2/`** *(213 GB)*  
Version 2 of the above dataset (same mice, updated acquisition or processing).

**`EndfootGeometry/`** *(1.8 GB)*  
Astrocyte endfoot morphology and vascular labeling data:
- Compressed Excel files with endfoot area vs. vessel diameter measurements (WT old vs. young, MLC1/GFAP/AQP4 staining)
- `Representative Images/` — 20x cortex scans
- `Alphafold_Prediction/` — protein structure prediction files

**`Viral_Tool_Core/`** *(18 GB)*  
Storage folder for Viral Tool Core shared files and Alphafold structure predictions. Currently contains only an `Alphafold_Prediction/` subfolder.

---
<!-- END:imaging -->

<!-- BEGIN:file-formats -->
### File format reference (Viewpoint recording system)

| Extension | Contents | Used? |
|---|---|---|
| `.exp` | Raw EEG + EMG (+ LFP as EEG3 in eeg2) | **Yes** — primary data |
| `.xls` with "quantitatif" | Sleep scores exported from Viewpoint | **Yes** — primary scores |
| `.xls` with "sleepscore" | Sleep scores formatted for MATLAB analysis | Yes |
| `.bin` | May contain sleep scores (extraction unsolved in MATLAB) | Rarely |
| `.csv` | Exported sleep scores | Sometimes |
| `.H` | Hypnogram file (Viewpoint internal) | No |
| `.act` | Actimetry | No |
| `.avi` | Video recording | Sometimes |

---
<!-- END:file-formats -->

<!-- BEGIN:misc-root -->
### Miscellaneous files in root

| File | Description |
|---|---|
| `readme.txt` | Overview of eeg1/eeg2 structure + sample MATLAB analysis script |
| `U19BlueHiveSetup.txt` | Setup notes for the BlueHive HPC cluster |
| `sleepscoringatCTN.pptx` | Presentation on sleep scoring workflow at CTN Copenhagen |
| `print_directory_content.sh` | Shell script to print folder tree |
| `gpu_debugging_example.sh` | Example GPU debugging script |
| `sleep_scoring_sample_data_20230615.zip` | Sample data package for sleep scoring (June 2023) |
| `M8_baseline.tif`, `M8_csd.tif`, `M8_kcl.tif` | Loose TIF files for mouse M8 (baseline, CSD, KCl) |

---
<!-- END:misc-root -->

<!-- BEGIN:experimenter-folders -->
### Experimenter folders

Personal folders for: `AaronTran/`, `devinwong/`, `fazla/` / `Fazla/`, `Felix/`, `jaysenSS/`, `Marta1/`, `Marta2/`, `marta_nwb/`, `Mike_G/`, `Mike_G-OLD/`, `New_data_by_Zuzanna_repeat_recording/`, `Tina/`, `Velia/`, `Yue/`.  
Contents vary — typically raw recordings, processed data, or analysis scripts specific to that person's experiments.
<!-- END:experimenter-folders -->
