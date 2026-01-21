# Multimodal MIMIC-IV-Ext-ECG-CXR-LVEF-benchmark Dataset

# Description
This repository provides the public dataset tables (CSV files) used in our work submitted to IEEE EMBC 2026 and currently **under review** processed from the MIMIC-IV dataset.

This dataset links Electrocardiograms (ECG) and Chest Radiographs (CXR) at the patient level under temporal constraints, and includes a binary label for **reduced LVEF** (low_lvef, LVEF < 50%) derived from unstructured clinical notes using a large language model (LLM) - GPT-OSS-20B.

- ECGs were obtained from the [MIMIC-IV-ECG dataset](https://physionet.org/content/mimic-iv-ecg/1.0/).
- CXR were obtained from the [MIMIC-IV-CXR-JPG dataset](https://physionet.org/content/mimic-cxr-jpg/2.0.0/).
- The clinical notes were obtained from the [MIMIC-IV-Note dataset](https://www.physionet.org/content/mimic-iv-note/2.2/).

## What is included

- `mimic_ecg_cxr_lvef_train_PUBLIC.csv` (our training dataset)
- `mimic_ecg_cxr_lvef_test_single_PUBLIC.csv` (our test dataset)

These files contain:
- temporally aligned ECG–CXR pairings (tables only)
- hashed identifiers that allow reproducibility for credentialed users of MIMIC-IV
- fold split information

The original MIMIC-IV identifiers were replaced by deterministic SHA-256 hashes.

The `low_lvef` label was created by extracting LVEF information from unstructured clinical notes using an LLM, mapping:
- quantitative LVEF values **< 50%** to `low_lvef = 1`, otherwise `0`
- qualitative descriptions consistent with systolic dysfunction to `low_lvef = 1`

Because labels were obtained via automated text extraction using a LLM, they should be treated as a **silver-standard** reference and may contain errors (e.g., occasional false negatives/positives due to ambiguous or indirect documentation).

## Files and columns

- `pair_uid`: hash for the full (ECG, CXR, note) association in the row
- `subject_id_uid`: hashed patient identifier
- `dicom_id_uid`: hashed CXR DICOM identifier
- `cxr_study_id_uid`: hashed CXR study identifier
- `ecg_study_id_uid`: hashed ECG study identifier
- `note_id_uid`: hashed note identifier
- `low_lvef`: binary target label (1 = reduced LVEF, 0 = not reduced)
- `LVEF`: numeric LVEF value when a quantitative value was extracted (may be missing for qualitative-only notes)
- `cxr_POCS0`, `cxr_POCS1`: CXR view/projection metadata as provided by the source tables
- `time_diff`: ECG–CXR time difference (relative)
- `time_diff_cxr_note`: time difference between CXR and the note providing LVEF documentation (relative)
- `time_diff_ecg_note`: time difference between ECG and the note providing LVEF documentation (relative)
- `fold` (train only): fold index for cross-validation
- `note_type`: note category/type label from source tables (no note text is provided)
- `note_seq`: note sequence field from source tables

## Test-set metrics 

The table below summarizes the performance reported on the independent test set defined by `mimic_ecg_cxr_lvef_test_single_PUBLIC.csv` in our EMBC 2026 submission. Values are reported as mean (95% bootstrap CI).

| Model | AUC | Sensitivity | Specificity | LR+ | LR- |
|---|---:|---:|---:|---:|---:|
| ECG only | 0.87 (0.76–0.95) | 0.65 (0.46–0.83) | 0.84 (0.69–0.96) | 5.43 (1.94–16.97) | 0.41 (0.20–0.68) |
| CXR only | 0.69 (0.54–0.83) | 0.58 (0.38–0.77) | 0.69 (0.50–0.87) | 2.08 (0.98–4.50) | 0.63 (0.33–1.01) |
| ECG + CXR (gated fusion) | 0.89 (0.79–0.96) | 0.77 (0.60–0.92) | 0.81 (0.65–0.95) | 4.90 (2.06–15.73) | 0.29 (0.10–0.53) |

## Citation

If you use this dataset tables in academic work, please cite our EMBC 2026 submission.

```bibtex
@inproceedings{Dias2026EMBC,
  title={Evaluating multimodal and unimodal models using electrocardiograms and chest radiographs for reduced ejection fraction detection},
  author={Dias, Felipe M and Cardenas, Diego A C and Ribeiro, Estela and Moreno, Ramon A and Rebelo, Marina F S and Krieger, Jose E and Gutierrez, Marco A},
  booktitle={2026 48th Annual International Conference of the IEEE Engineering in Medicine and Biology Society (EMBC)},
  year={2026},
  pages={1--4},
  organization={IEEE},
  note= {Submitted - under review}
}
```

Also, please cite the original MIMIC-IV

```bibtex
@misc{johnson2024mimic,
  title={MIMIC-IV (Version 3.1). PhysioNet. RRID: SCR\_007345},
  author={Johnson, A and Bulgarelli, L and Pollard, T and Gow, B and Moody, B and Horng, S and Celi, LA and Mark, R},
  year={2024}
}

@article{johnson2023mimic,
  title={MIMIC-IV, a freely accessible electronic health record dataset},
  author={Johnson, Alistair EW and Bulgarelli, Lucas and Shen, Lu and Gayles, Alvin and Shammout, Ayad and Horng, Steven and Pollard, Tom J and Hao, Sicheng and Moody, Benjamin and Gow, Brian and others},
  journal={Scientific data},
  volume={10},
  number={1},
  pages={1},
  year={2023},
  publisher={Nature Publishing Group UK London}
}

@article{goldberger2000physiobank,
  title={PhysioBank, PhysioToolkit, and PhysioNet: components of a new research resource for complex physiologic signals},
  author={Goldberger, Ary L and Amaral, Luis AN and Glass, Leon and Hausdorff, Jeffrey M and Ivanov, Plamen Ch and Mark, Roger G and Mietus, Joseph E and Moody, George B and Peng, Chung-Kang and Stanley, H Eugene},
  journal={circulation},
  volume={101},
  number={23},
  pages={e215--e220},
  year={2000},
  publisher={Lippincott Williams \& Wilkins}
}
```
