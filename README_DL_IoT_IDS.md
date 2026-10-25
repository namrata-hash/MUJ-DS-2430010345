# Deep Learning Based Intrusion Detection for IoT Networks

## Project README --- Current Experimental Baseline

> **Important:** This README documents the **current experiment only**.\
> Previous experiments involving **1D CNN** and the earlier Transformer
> results are no longer part of the project baseline.\
> The current comparison uses **GRU in place of 1D CNN** and the
> **improved Transformer results**.

------------------------------------------------------------------------

## 1. Project Overview

This project investigates deep learning architectures for **multiclass
intrusion detection in IoT network traffic** using the **CICIoT2023
dataset**.

The objective is to compare different neural-network architecture
families under the same experimental setup and determine the trade-off
between:

-   predictive performance,
-   class-balanced performance, and
-   computational/training cost.

The current experiment evaluates seven models:

1.  DNN
2.  DNN + Dropout
3.  DNN + L2
4.  Proposed DNN
5.  GRU
6.  LSTM
7.  Transformer

The evaluation uses:

-   Accuracy
-   Macro Precision
-   Macro Recall
-   Macro F1
-   Training Time

------------------------------------------------------------------------

## 2. Current Project Status

**Status:** Current baseline established

**Current model set:**

  Architecture family   Models
  --------------------- --------------------------------------------
  Feed-forward          DNN, DNN + Dropout, DNN + L2, Proposed DNN
  Recurrent             GRU, LSTM
  Attention-based       Transformer

### Current overall findings

-   **Best overall model:** DNN
-   **Best accuracy:** DNN --- 75.44%
-   **Best macro F1:** DNN --- 63.46%
-   **Best macro precision:** Transformer --- 65.39%
-   **Best macro recall:** Transformer --- 71.05%
-   **Fastest model:** DNN + L2 --- 18.46 seconds
-   **Best recurrent model:** GRU
-   **Highest training cost:** Transformer --- 3,497.91 seconds

These results are the authoritative baseline for subsequent project
documentation, presentations, analysis, and experiments.

------------------------------------------------------------------------

## 3. Dataset and Experimental Setup

### 3.1 Dataset

**Dataset:** CICIoT2023

The experiment uses an eight-class configuration derived from the
CICIoT2023 dataset.

### 3.2 Dataset Statistics

  Item                                 Current configuration
  ------------------------------------ ----------------------------
  Dataset context                      CICIoT2023
  Supplied training flows              1,073,851
  Test flows                           268,463
  Numeric input features               39
  Number of classes                    8
  Balanced training pool               80,000 samples
  Samples per class in training pool   10,000
  Train/validation split               80/20 stratified
  Feature scaling                      StandardScaler
  Test distribution                    Natural class distribution

### 3.3 Data Preparation

The current preprocessing pipeline is:

1.  Load the supplied CICIoT2023 training and test data.
2.  Remove **84,370 exact duplicate training records**.
3.  Select the current eight-class classification problem.
4.  Balance the training pool to **10,000 samples per class**.
5.  Obtain a total training pool of **80,000 samples**.
6.  Perform an **80/20 stratified train-validation split**.
7.  Fit `StandardScaler` on the training portion.
8.  Apply the fitted scaler to validation and test data.
9.  Train and evaluate each model using the same task configuration.

### 3.4 Target Classes

The current eight classes are:

1.  Benign
2.  BruteForce
3.  DDoS
4.  DoS
5.  Mirai
6.  Recon
7.  Spoofing
8.  Web-based

------------------------------------------------------------------------

## 4. Feature Representation

The experiment uses **39 standardized numerical network-flow features**.

### Feed-forward models

The DNN-family models receive the 39-dimensional feature vector
directly.

### Recurrent models

For GRU and LSTM, the 39 features are represented as a sequence with one
feature value per timestep.

> This is a modelling representation. The 39 features do not represent a
> naturally observed temporal sequence.

### Transformer

The Transformer converts the 39 features into feature tokens and
processes their interactions using self-attention.

------------------------------------------------------------------------

## 5. Models Evaluated

## 5.1 DNN

The DNN is the primary feed-forward baseline.

**Purpose:** Establish a simple nonlinear reference model.

**Conceptual structure:**

``` text
39 input features
        ↓
Dense(128, ReLU)
        ↓
Dense(64, ReLU)
        ↓
Dense(32, ReLU)
        ↓
Softmax(8)
```

------------------------------------------------------------------------

## 5.2 DNN + Dropout

This model extends the baseline DNN by adding dropout regularisation.

**Purpose:** Test whether stochastic regularisation improves
generalisation.

``` text
39 features
    ↓
Dense(128)
    ↓
Dropout
    ↓
Dense(64)
    ↓
Dropout
    ↓
Dense(32)
    ↓
Softmax(8)
```

------------------------------------------------------------------------

## 5.3 DNN + L2

This model adds L2 weight regularisation to the dense layers.

**Purpose:** Evaluate whether penalising large weights improves
generalisation while maintaining a lightweight architecture.

``` text
39 features
    ↓
Dense(128) + L2
    ↓
Dense(64) + L2
    ↓
Dense(32)
    ↓
Softmax(8)
```

------------------------------------------------------------------------

## 5.4 Proposed DNN

The Proposed DNN combines multiple regularisation and stabilisation
mechanisms.

It uses:

-   L2 regularisation
-   Batch Normalization
-   Dropout
-   Multiple dense layers

**Purpose:** Test whether combining regularisation strategies provides a
better-performing dense architecture.

------------------------------------------------------------------------

## 5.5 GRU

GRU is the recurrent architecture used in the **current experiment**.

> **GRU replaces the previous 1D CNN model in the current project
> baseline.**

**Purpose:** Evaluate recurrent sequence processing as an alternative to
the feed-forward models.

Conceptually:

``` text
39 × 1 sequence
      ↓
GRU(64)
      ↓
Dropout
      ↓
GRU(32)
      ↓
Dense(64)
      ↓
Dropout
      ↓
Softmax(8)
```

------------------------------------------------------------------------

## 5.6 LSTM

LSTM provides a second recurrent architecture for comparison.

**Purpose:** Evaluate a more expressive gated-memory mechanism against
GRU.

Conceptually:

``` text
39 × 1 sequence
      ↓
LSTM(64)
      ↓
Dropout
      ↓
LSTM(32)
      ↓
Dropout
      ↓
Dense(64)
      ↓
Softmax(8)
```

------------------------------------------------------------------------

## 5.7 Transformer

The Transformer is the attention-based architecture in the current
experiment.

**Purpose:** Evaluate self-attention for modelling interactions among
feature tokens.

The improved Transformer uses:

-   feature tokenization,
-   learnable positional information,
-   a learnable CLS token,
-   Transformer encoder blocks,
-   multi-head self-attention,
-   Layer Normalization,
-   a dense classification head.

Conceptually:

``` text
39 numerical features
        ↓
Feature Tokenizer
        ↓
Learnable Position Embeddings
        ↓
CLS Token
        ↓
Transformer Encoder Block
        ↓
Transformer Encoder Block
        ↓
Layer Normalization
        ↓
CLS Representation
        ↓
Dense Classification Head
        ↓
Softmax(8)
```

------------------------------------------------------------------------

## 6. Evaluation Metrics

### Accuracy

Measures the percentage of all predictions that are correct.

### Macro Precision

Calculates precision independently for every class and then gives every
class equal weight.

### Macro Recall

Calculates recall independently for every class and averages the
class-level values.

### Macro F1

Provides a balanced measure of precision and recall across the eight
classes.

### Training Time

Measures the recorded training time for each model in seconds.

> Training time is used as a relative computational-cost indicator. It
> should **not** be interpreted as deployment inference latency.

------------------------------------------------------------------------

## 7. Final Experimental Results

  ------------------------------------------------------------------------------------------
        Rank Model             Accuracy        Macro Macro Recall     Macro F1 Training Time
                                           Precision                                     (s)
  ---------- ------------- ------------ ------------ ------------ ------------ -------------
           1 **DNN**         **75.44%**       64.03%       70.31%   **63.46%**         19.48

           2 **DNN + L2**        75.08%       63.43%       68.96%       63.05%     **18.46**

           3 Proposed DNN        74.07%       64.51%       68.98%       62.26%         25.70

           4 DNN + Dropout       73.73%       63.91%       69.02%       62.10%         21.84

           5 Transformer         72.53%   **65.39%**   **71.05%**       62.63%   **3497.91**

           6 GRU                 69.82%       60.34%       65.51%       58.68%        668.69

           7 LSTM                55.24%       52.20%       51.43%       45.71%        173.61
  ------------------------------------------------------------------------------------------

------------------------------------------------------------------------

## 8. Results Interpretation

### 8.1 DNN --- Overall Performance Leader

The DNN achieves:

-   **75.44% accuracy**
-   **63.46% macro F1**
-   64.03% macro precision
-   70.31% macro recall
-   19.48 seconds training time

It is the strongest overall model when predictive performance and
computational cost are considered together.

------------------------------------------------------------------------

### 8.2 DNN + L2 --- Efficiency Leader

DNN + L2 achieves:

-   75.08% accuracy
-   63.05% macro F1
-   **18.46 seconds training time**

It is only slightly below the DNN in accuracy and macro F1 while being
the fastest model.

This makes DNN + L2 a strong candidate when retraining efficiency is
important.

------------------------------------------------------------------------

### 8.3 Proposed DNN

The Proposed DNN achieves:

-   74.07% accuracy
-   62.26% macro F1
-   64.51% macro precision
-   68.98% macro recall

It remains competitive but does not outperform the simpler DNN or DNN +
L2 in the current run.

------------------------------------------------------------------------

### 8.4 DNN + Dropout

DNN + Dropout achieves:

-   73.73% accuracy
-   62.10% macro F1

The selected dropout configuration does not improve the baseline DNN in
this experiment.

This should not be interpreted as evidence that dropout is ineffective
in general; it only describes the current configuration and run.

------------------------------------------------------------------------

### 8.5 GRU --- Best Recurrent Model

GRU achieves:

-   **69.82% accuracy**
-   **58.68% macro F1**
-   60.34% macro precision
-   65.51% macro recall
-   668.69 seconds training time

GRU clearly outperforms LSTM in the current comparison.

However, it requires substantially more training time than the
DNN-family models.

------------------------------------------------------------------------

### 8.6 LSTM

LSTM achieves:

-   55.24% accuracy
-   45.71% macro F1
-   52.20% macro precision
-   51.43% macro recall
-   173.61 seconds training time

It is the weakest model in the current comparison based on accuracy and
macro F1.

------------------------------------------------------------------------

### 8.7 Transformer --- Strongest Macro Precision and Recall

The improved Transformer achieves:

-   72.53% accuracy
-   **65.39% macro precision**
-   **71.05% macro recall**
-   62.63% macro F1
-   **3,497.91 seconds training time**

The Transformer therefore does not lead in accuracy or macro F1, but it
achieves the best macro precision and macro recall.

Its main disadvantage is computational cost.

------------------------------------------------------------------------

## 9. Model Rankings

### Accuracy

1.  DNN --- 75.44%
2.  DNN + L2 --- 75.08%
3.  Proposed DNN --- 74.07%
4.  DNN + Dropout --- 73.73%
5.  Transformer --- 72.53%
6.  GRU --- 69.82%
7.  LSTM --- 55.24%

### Macro F1

1.  DNN --- 63.46%
2.  DNN + L2 --- 63.05%
3.  Transformer --- 62.63%
4.  Proposed DNN --- 62.26%
5.  DNN + Dropout --- 62.10%
6.  GRU --- 58.68%
7.  LSTM --- 45.71%

### Training Efficiency

1.  DNN + L2 --- 18.46 s
2.  DNN --- 19.48 s
3.  DNN + Dropout --- 21.84 s
4.  Proposed DNN --- 25.70 s
5.  LSTM --- 173.61 s
6.  GRU --- 668.69 s
7.  Transformer --- 3497.91 s

------------------------------------------------------------------------

## 10. Main Findings

### Finding 1 --- Simpler architecture performs best overall

The DNN achieves the highest accuracy and macro F1.

### Finding 2 --- L2 provides an excellent efficiency/performance trade-off

DNN + L2 is the fastest model while remaining extremely close to the
DNN.

### Finding 3 --- Transformer excels on macro precision and recall

The Transformer records the highest macro precision and macro recall,
showing that accuracy alone does not describe every aspect of multiclass
IDS performance.

### Finding 4 --- GRU is stronger than LSTM

GRU substantially outperforms LSTM in the current experiment.

### Finding 5 --- Higher complexity increases training cost

The recurrent and Transformer architectures require substantially more
training time than the DNN-family models.

### Finding 6 --- Complexity does not guarantee better performance

The Transformer is the most computationally expensive model but does not
achieve the highest accuracy or macro F1.

------------------------------------------------------------------------

## 11. Recommended Interpretation for the Project

The results should be presented as a **controlled architecture
comparison**, not as a universal claim that one neural-network
architecture is always superior.

The current evidence supports the following interpretation:

> **DNN is the strongest overall model in the current experiment, DNN +
> L2 is the most efficient model, GRU is the strongest recurrent model,
> and the improved Transformer provides the strongest macro precision
> and recall at a substantially higher computational cost.**

------------------------------------------------------------------------

## 12. Important Experimental Limitations

The current results have several limitations:

-   The comparison represents **one experimental run**.
-   The test set retains its **natural class distribution**.
-   Training uses a **balanced 80,000-sample pool** rather than the
    entire supplied training set.
-   No inference-latency measurement is included.
-   GRU and Transformer reached their epoch limits while validation loss
    was still falling, so further tuning could change their results.
-   Small differences between models should not be treated as
    statistically established architectural advantages.
-   No complete deployment benchmark for memory, throughput or energy is
    included.
-   The GRU/LSTM sequence representation is a modelling choice rather
    than a naturally temporal ordering of the 39 features.
-   Published CICIoT2023 results should not be directly treated as
    comparable SOTA results unless the task definition, classes,
    preprocessing, sampling, split and evaluation protocol are aligned.

------------------------------------------------------------------------

## 13. State-of-the-Art Comparison Guidance

Published studies may report much higher accuracy than the current
experiment, but their results can use:

-   different numbers of classes,
-   different subsets,
-   different train/test splits,
-   different balancing methods,
-   different feature sets,
-   binary instead of multiclass classification,
-   different evaluation metrics.

Therefore:

> **Do not claim that the current 75.44% DNN result is the State of the
> Art for CICIoT2023.**

The correct claim is that the project provides a **controlled
seven-model comparison under the current eight-class experimental
configuration**.

------------------------------------------------------------------------

## 14. Project Deliverables

The current project should maintain the following artifacts:

``` text
Project/
│
├── README_Results.md
│
├── Final_Model_Results.csv
├── Final_Model_Results.xlsx
│
├── Model_Architecture_Documentation_Current.txt
│
├── Deep_Learning_Project_Report_Current_GRU_Transformer.docx
│
├── Notebook/
│   └── Current_Final_Experiment.ipynb
│
├── Results/
│   ├── model_metrics.csv
│   ├── training_history/
│   ├── confusion_matrices/
│   └── plots/
│
└── Documentation/
    ├── dataset_description.md
    ├── model_architecture.md
    └── experiment_notes.md
```

------------------------------------------------------------------------

## 15. Reproducibility Checklist

Before considering the experiment final, record:

-   [ ] Exact dataset files
-   [ ] Exact feature list
-   [ ] Exact class mapping
-   [ ] Duplicate-removal procedure
-   [ ] Sampling/balancing procedure
-   [ ] Train-validation split
-   [ ] Random seed
-   [ ] StandardScaler configuration
-   [ ] Model architecture
-   [ ] Optimizer
-   [ ] Learning rate
-   [ ] Batch size
-   [ ] Number of epochs
-   [ ] Early stopping configuration
-   [ ] Hardware/environment
-   [ ] Training time
-   [ ] Test metrics
-   [ ] Per-class metrics
-   [ ] Confusion matrix

------------------------------------------------------------------------

## 16. Future Work

The next stage of the project should focus on:

1.  Per-class precision, recall and F1 analysis.
2.  Confusion matrices for all seven models.
3.  Hyperparameter tuning.
4.  Class weighting and focal-loss experiments.
5.  Better imbalance-aware evaluation.
6.  Cross-dataset generalisation.
7.  Inference latency and throughput measurement.
8.  Memory and energy profiling.
9.  Explainability using SHAP/LIME or related techniques.
10. Robustness against distribution shifts and adversarial
    perturbations.
11. Lightweight models for edge deployment.
12. Statistical validation using repeated runs.

------------------------------------------------------------------------

## 17. Final Baseline Summary

  Category                     Current winner
  ---------------------------- -------------------------------
  Overall performance          **DNN**
  Accuracy                     **DNN --- 75.44%**
  Macro F1                     **DNN --- 63.46%**
  Macro Precision              **Transformer --- 65.39%**
  Macro Recall                 **Transformer --- 71.05%**
  Training efficiency          **DNN + L2 --- 18.46 s**
  Recurrent model              **GRU --- 69.82% accuracy**
  Highest computational cost   **Transformer --- 3497.91 s**

------------------------------------------------------------------------

## 18. Final Conclusion

The current experiment demonstrates that increasing neural-network
architectural complexity does not automatically result in better overall
intrusion-detection performance.

The **DNN is the overall performance leader**, achieving the highest
accuracy and macro F1 while maintaining a low training cost. **DNN +
L2** provides the best training efficiency and remains very close to the
DNN in predictive performance. **GRU** is the stronger recurrent
architecture compared with LSTM. The **improved Transformer** is
particularly strong in macro precision and macro recall, but its
substantially higher training cost creates an important
performance-efficiency trade-off.

These results form the **current project baseline** and should be used
consistently in the project report, presentation, analysis, and future
experiments.
