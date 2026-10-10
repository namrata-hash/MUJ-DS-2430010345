# CICIoT2023 Deep Learning Intrusion Detection Project

## Student
- Name: Namrata Kothari
- Registration Number: __________________
- Section: __________________

## Project
Comparative Analysis of Deep Learning Models for IoT Intrusion Detection Using CICIoT2023

## Models
1. DNN
2. DNN + Dropout
3. DNN + L2
4. Proposed DNN: Dropout + L2 + Batch Normalization + adaptive training
5. 1D CNN
6. LSTM

## Dataset
CICIoT2023, 8-class grouped attack classification using 39 numerical flow features.

## Experimental setup
- Duplicate removal before training sampling
- 10,000 samples per class for the balanced training pool
- 64,000 train / 16,000 validation
- Original imbalanced test set: 268,463 rows
- StandardScaler fitted on training data only
- Random seed: 42
- Batch size: 256
- Maximum epochs: 20
- Optimizer: Adam
- Loss: sparse categorical cross-entropy

## Main findings
- Best accuracy: DNN = 75.79%
- Best macro-F1: 1D CNN = 0.6356
- LSTM = 51.48% accuracy
- Proposed DNN = 73.00% accuracy
- The proposed regularization combination did not outperform the baseline under this setup.

## Research gap
The project addresses the need for a controlled and reproducible comparison of representative DL architectures on CICIoT2023 using a common preprocessing/evaluation pipeline, with explicit attention to class imbalance, regularization and computational cost.

## Important limitation
Published SOTA results use different preprocessing, class definitions, sampling and evaluation protocols. Therefore, external SOTA numbers are contextual and should not be interpreted as strict head-to-head comparisons.

## Documentation
- `CICIoT2023_Final_Project_Report_Updated.docx`
- `documentation/CICIoT2023_Literature_Review.docx`
- `documentation/Research_Gap_and_Limitations.docx`

## Notebook
`notebooks/DeepLearningFinal.ipynb`

## Reproducibility
Install dependencies using `requirements.txt`.

## SOTA comparison
The submission now includes `documentation/CICIoT2023_SOTA_Comparison_Detailed.docx`, containing:
- a detailed CICIoT2023 multiclass SOTA table,
- a full multiclass benchmark table from Yaras & Dener (2024),
- direct comparison with the project's DNN/CNN/LSTM models,
- comparability notes,
- interpretation of the SOTA gap,
- research gap derived from the comparison.

## Dataset documentation
The final report includes an expanded dataset appendix covering:
- dataset size,
- 42 original columns,
- 39 model input features,
- 8 grouped classes,
- class distribution,
- duplicate removal,
- balanced training pool,
- train/validation/test distribution,
- leakage-safe preprocessing.

## Complete SOTA model comparison
`documentation/CICIoT2023_Complete_Model_by_Model_Comparison.docx` contains the complete model-by-model comparison of the supplied research-paper models against all six models implemented in this project.
