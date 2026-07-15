# Predicting Gait Speed from EEG and EMG Signals

A machine learning project that classifies walking speed (slow / medium / fast) from synchronized EEG (brain) and EMG (muscle) signals — the foundational classifier for a future brain- and muscle-driven wheelchair speed controller.

Dataset: [*A Multimodal Gait Dataset of Brain Activity, Muscle Activity, Kinematics and Ground Forces in Young Adults*](https://physionet.org/content/multimodal-gait-dataset/1.0.0/) (PhysioNet, 2026).

---

## Project Structure

```
Project/
├── README.md                          # this file
├── gait_speed_eeg_emg_pipeline.ipynb  # main notebook: EDA, preprocessing, feature extraction
├── gait_features_clean.csv            # generated output: one row per epoch, 93 features + label
├── DATASET_README.md     
└── Dataset/
    ├── subject-info.csv               # participant demographics (gender, BMI category, etc.)
    ├── EEG_Data/
    │   └── EEG.csv/                   # 177 files: S<id>_<speed>_raw.csv
    └── EMG_Data/                      # 169 files: EMG_S<id>_<speed>.csv
```

> **Note on folder naming:** in this project's actual layout, the EEG `.csv` files sit inside a folder literally named `EEG.csv` (`Dataset/EEG_Data/EEG.csv/S1_0.5_raw.csv`, etc.) — this is just how the dataset was unzipped, not a typo. The notebook's auto-detection cell finds this automatically; you don't need to rename anything.

Not currently used by the notebook, but included in the raw dataset if you download it directly from PhysioNet: `IMU.tar.gz` (kinematics) and force-plate `FP_*.csv` files. This project only uses the EEG and EMG modalities.

---

## Requirements

- Python 3.9+
- Jupyter (via VS Code's Jupyter extension, or `jupyter notebook` / `jupyter lab`)

Install dependencies:

```bash
pip install pandas numpy scipy scikit-learn matplotlib seaborn imbalanced-learn
```

If a cell later imports something not in this list (e.g. `xgboost`, `lightgbm` for the modeling stage), install it the same way when you reach that point.

---

## How to Run

1. **Place the dataset** so the folder structure above is under a `Dataset/` folder in the same directory as the notebook (or anywhere — the notebook searches recursively for the expected filenames, see step 3).

2. **Open the notebook** in VS Code or Jupyter:
   ```bash
   jupyter notebook gait_speed_eeg_emg_pipeline.ipynb
   ```

3. **Run cells top to bottom, in order — do not skip around.** The very first code cell auto-detects `EEG_DIR` and `EMG_DIR` by searching for the known filename patterns (`S*_raw.csv`, `EMG_S*.csv`). It should print:
   ```
   EEG files found: 177 (expect 177)
   EMG files found: 169 (expect 169)
   ```
   If either count is wrong or 0, the dataset isn't where the notebook expects — check the printed `EEG_DIR`/`EMG_DIR` paths and move the data (or edit `SEARCH_ROOT`) accordingly before continuing.

4. **Whenever you edit a function cell** (e.g. `load_clean_pair`), re-run that cell before re-running anything below it. Jupyter keeps whatever was executed *last* in memory, not what's currently in the cell — if something behaves unexpectedly after an edit, use **Restart Kernel → Run All** to guarantee a clean state.

5. **Sections, in order:**
   | Section                        | What it does                                                                              |
   |--------------------------------|-------------------------------------------------------------------------------------------|
   | 1. Folder verification & setup | Locates data, defines paths, subject/speed lists                                          |
   | 2. Data Understanding (EDA)    | Inspects shapes, channels, dtypes, missing values, duplicates, class balance — read-only  |
   | 3. Data Preprocessing          | Cleans, filters (EEG bandpass, EMG smoothing), encodes labels, sets up scaling,           |
   |                                |   checks imbalance, epochs the signals                                                    |
   | 4. Feature Extraction          | Loops over all valid subject-speed pairs, extracts EEG band-power + EMG RMS/MAV/          |
   |                                |  waveform-length features, builds `features_df`, exports `gait_features_clean.csv`        |
   | 5. Post-feature-extraction     | Correlation heatmap, X/y split, SMOTE/TomekLinks/SMOTETomek evaluation                    |
   |    checks                      |                                                                                             

6. **Expected final output:** `features_df` with shape approximately `(18774, 95)` — 168 valid subject-speed pairs (169 minus one file with a corrupted channel-label header, subject S32 at 1.0 m/s), yielding 18,774 one-second epochs across 93 extracted features plus `subject` and `label` columns.

7. The exported `gait_features_clean.csv` is the reproducible checkpoint — model training (Random Forest, SVM, Gradient Boosting, k-NN, Logistic Regression) picks up from this file rather than re-running the full signal pipeline each time.

---

## Known Data Issues (already handled in the notebook)

- **8 EMG files missing** from the source dataset: `S3_0.75, S3_1, S8_0.5, S11_1, S43_1, S50_0.5, S50_0.75, S50_1` — automatically excluded via the `MISSING_EMG` set.
- **1 EEG file with a corrupted header** (`S32_1`): duplicate `A2` channel label and unlabeled `?`/`???` channels, likely an acquisition-software logging fault — automatically detected and skipped via an assertion check inside `load_clean_pair`.
- **EMG data is pre-processed** by the dataset provider (already Mean Absolute Value / MAV, not raw voltage) — the notebook applies light smoothing only, not a raw-EMG bandpass + rectification pipeline.

---

## Troubleshooting

- **`ModuleNotFoundError`** → install the missing package with `pip install <name>`, then restart the kernel.
- **All pairs skipped / `features_df` shape `(0, 0)`** → almost always a stale working directory or a leftover old function definition. Restart Kernel → Run All from the top.
- **`AssertionError: Expected EEG channels not found`** → a specific file has a non-standard channel layout (as with `S32_1`); this is expected to happen occasionally and is logged, not a bug to fix.