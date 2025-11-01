# 🩸 PPG-to-CBC Prediction: A Work in Progress

> **Status:** 🚧 *Active Development / Research Prototype*

This repository hosts the ongoing development of a proof-of-concept pipeline that predicts **Complete Blood Count (CBC)** laboratory test results from **photoplethysmography (PPG)** signals.  
The work is motivated by the potential of non-invasive, continuous physiological monitoring to approximate key hematological biomarkers.

---

## 🧠 Overview

The project uses raw **PPG waveforms** extracted from the **MIMIC-III** database to train machine learning models that estimate CBC test values such as **Hemoglobin, Hematocrit, WBC, and RBC counts**.  
It is currently implemented as a modular workflow composed of four main stages:

1. **`mat_modifier`** – Extracts relevant signals (`PPG_Raw`, metadata) from large `.mat` waveform files and stores them in reduced-size `.mat` files for downstream analysis.  
   - Built using `mat73` and `h5py`  
   - Supports key normalization and manifest tracking  

2. **`labs_data_cleaning`** – Processes and cleans laboratory data from MIMIC-III `LABEVENTS.csv` and `D_LABITEMS.csv`.  
   - Uses a **streaming** (chunked) pipeline capable of handling 18GB+ CSV files  
   - Keeps only **CBC-related** tests and flags results as:  
     - `1` → *Abnormal*  
     - `0` → *Normal* (no flag)  
   - Standardizes `SUBJECT_ID` → `pXXXXXX` format for waveform alignment  
   - Normalizes label variants (e.g., *“Red Blood Cells”* → `RBC`, *“White Blood Cells”* → `WBC`)  
   - Includes only results with sampling fluid = `"blood"`

3. **`lab_signal_matcher`** *(in progress)* – Matches laboratory timestamps to PPG time windows (±15 minutes).  
   - Will produce synchronized CSVs of PPG segments and lab results per subject  

4. **`notebook_scheduler`** *(planned)* – Automates the execution of per-test notebooks (e.g., `hematocrit_resnet50.ipynb`) and collates final metrics and plots.

---

## ⚙️ Current Architecture

- **Language:** Python 3.11  
- **Core Dependencies:** `pandas`, `numpy`, `mat73`, `h5py`, `yaml`, `tqdm`  
- **Data Source:** [MIMIC-III Clinical Database](https://physionet.org/content/mimiciii/1.4/), [PulseDB 2.0](https://github.com/pulselabteam/PulseDB), and (VitalDB)[https://vitaldb.net/dataset/]  
- **Input Size:** ~18 GB (lab data) + waveform `.mat` files  
- **Processing Mode:** Chunked / memory-safe streaming

---

## 🧩 Folder Structure (Work in Progress)

project_root/
│
├── configs/
│ ├── mat_modifier.yaml
│ └── labs_cleaning.yaml
│
├── data/
│ ├── raw/
│ │ ├── LABEVENTS.csv 
│ │ ├── D_LABITEMS.csv
│ │ └── mimic_waveforms/
│ ├── clean/
│ │ └── labs_cbc_clean.csv
│ └── reduced_waveforms/
│
├── notebooks/
│ ├── mat_modifier_param.ipynb
│ ├── labs_data_cleaning_param.ipynb
│ ├── lab_signal_matcher_param.ipynb # (to be patched)
| ├── notebook_scheduler.ipynb
| ├── models/
| └──  └── hematocrit_resnet50.ipynb # example model
│ 
└── scripts/ # generated later from notebooks

## 📈 Current Status

- [x] PPG waveform extraction pipeline functional (`mat_modifier.ipynb`)  
- [x] Large-scale lab data cleaner operational (`labs_data_cleaning_param.ipynb`)  
- [x] CBC test harmonization (`RBC`/`WBC` variants handled)  
- [ ] Lab-to-signal temporal matching (in progress)  
- [ ] Model training and evaluation notebooks (upcoming)

---

## 🧪 Upcoming Steps

- Integration of **lab-signal matcher** with ±15 min windowing  
- Feature extraction from **PPG** segments (time & frequency domain)  
- Training **deep neural networks (e.g., ResNet-50)** on combined PPG + metadata  
- Evaluation of **correlation and predictive accuracy** per CBC parameter  
- Packaging for **reproducible publication and release on GitHub**

---

## 👤 Author & Research Context

Developed by **Abdalrhman Mostafa** as part of an exploratory research direction at the intersection of **biomedical signal processing**, **clinical data science**, and **machine learning**.  
This repository represents **a live prototype**, not a finalized model.

> ⚠️ *Note:* The project is a **work in progress** and is not yet validated for clinical or diagnostic use.

---
