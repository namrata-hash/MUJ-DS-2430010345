# Deep Learning Based IoT Intrusion Detection using CICIoT2023

## Student Details

- **Name:** Namrata Kothari
- **Registration Number:** 2430010345
- **Section:** D
- **Institute:** Manipal University Jaipur
- **Subject:** Deep Learning

---

## 1. Project Overview

This project investigates the use of deep learning models for **multiclass IoT intrusion detection** using the **CICIoT2023** dataset.

The objective is to classify network traffic into eight attack/traffic categories and compare different deep learning architectures under the same experimental setting.

The project evaluates seven models:

1. DNN Baseline
2. DNN + Dropout
3. DNN + L2 Regularization
4. Proposed DNN (Dropout + L2 + Batch Normalization)
5. 1D CNN
6. LSTM
7. Transformer Encoder

The final experiment focuses on comparing predictive performance and computational cost using Accuracy, Macro Precision, Macro Recall, Macro F1 Score, Macro Specificity, and Training Time.

---

## 2. Problem Statement

IoT networks generate large volumes of heterogeneous network traffic and are exposed to attacks such as DDoS, DoS, Mirai, reconnaissance, spoofing, brute-force attacks, and web-based attacks.

Traditional intrusion detection approaches may struggle to capture complex nonlinear relationships in high-dimensional network traffic. This project therefore studies whether deep learning architectures can learn useful representations of network-flow features and distinguish multiple IoT attack categories.

The central research question is:

> **Which deep learning architecture provides the best balance between multiclass intrusion-detection performance and training cost on the CICIoT2023 dataset under a common experimental setup?**

---

## 3. Dataset

### Dataset Name

**CICIoT2023**

### Dataset Used

The project uses the provided CICIoT2023 Parquet training and testing files:

- `train-00000-of-00001.parquet`
- `test-00000-of-00001.parquet`

### Dataset Size

The notebook reports:

| Dataset | Samples | Columns |
|---|---:|---:|
| Training data | 1,073,851 | 42 |
| Test data | 268,463 | 42 |

The training data contains **8 attack/traffic classes**.

### Original Class Distribution

The original training distribution is imbalanced:

| Class | Samples |
|---|---:|
| DDoS | 361,842 |
| Recon | 161,877 |
| Benign | 159,990 |
| DoS | 159,925 |
| Mirai | 119,897 |
| Spoofing | 80,043 |
| Web-based | 19,820 |
| BruteForce | 10,457 |

### Final Classes

The eight classes used in the experiment are:

- Benign
- BruteForce
- DDoS
- DoS
- Mirai
- Recon
- Spoofing
- Web-based

---

## 4. Data Preprocessing

The notebook performs the following preprocessing steps.

### 4.1 Duplicate Removal

Exact duplicate rows are removed from the training data.

- Rows before duplicate removal: **1,073,851**
- Rows after duplicate removal: **989,481**
- Duplicates removed: **84,370**

### 4.2 Class Balancing

Because the original training distribution is highly imbalanced, the project uses balanced sampling.

A maximum of **10,000 samples per class** is selected using:

- `random_state = 42`

This produces:

- **80,000 balanced training samples**
- **10,000 samples per class**

### 4.3 Feature Selection

The target column is:

`attack_class`

The following label columns are removed from the input:

- `attack_class`
- `Label`
- `label`

Only numerical flow features are retained.

Final input dimensionality:

**39 numerical features**

### 4.4 Label Encoding

`LabelEncoder` is used to convert the eight class names into integer labels:

```text
0 = Benign
1 = BruteForce
2 = DDoS
3 = DoS
4 = Mirai
5 = Recon
6 = Spoofing
7 = Web-based
```

### 4.5 Train–Validation Split

The balanced training set is divided using a stratified split:

- Training: **64,000 samples**
- Validation: **16,000 samples**
- Validation ratio: **20%**
- Random state: **42**

### 4.6 Feature Scaling

`StandardScaler` is fitted on the training data and then applied to:

- Training data
- Validation data
- Test data

The same fitted scaler is therefore used for all evaluation data.

### 4.7 Test Set

The original test set is retained for final evaluation:

- **268,463 samples**
- **39 numerical features**

---

## 5. Models

Seven deep learning architectures are evaluated.

### 5.1 DNN Baseline

The baseline feed-forward neural network contains:

```text
Input: 39 features
Dense(128, ReLU)
Dense(64, ReLU)
Dense(32, ReLU)
Dense(8, Softmax)
```

### 5.2 DNN + Dropout

Dropout regularization is added after the first two hidden layers.

- Dropout rate: **0.30**

### 5.3 DNN + L2

L2 kernel regularization is applied to the first two dense layers.

- L2 coefficient: **0.001**

### 5.4 Proposed DNN

The proposed DNN combines:

- Dense layers
- Batch Normalization
- Dropout
- L2 regularization

The architecture uses:

```text
Dense(128, ReLU, L2=0.001)
Batch Normalization
Dropout(0.30)
Dense(64, ReLU, L2=0.001)
Dropout(0.30)
Dense(32, ReLU)
Dropout(0.20)
Dense(8, Softmax)
```

### 5.5 1D CNN

The CNN treats the feature vector as a one-dimensional sequence.

Main components:

```text
Conv1D(64, kernel_size=3)
Batch Normalization
MaxPooling1D
Conv1D(128, kernel_size=3)
MaxPooling1D
Flatten
Dense(128, ReLU)
Dropout(0.30)
Dense(8, Softmax)
```

### 5.6 LSTM

The LSTM processes the feature sequence using recurrent layers:

```text
LSTM(64, return_sequences=True)
Dropout(0.30)
LSTM(32)
Dropout(0.30)
Dense(64, ReLU)
Dense(8, Softmax)
```

### 5.7 Transformer Encoder

A lightweight Transformer encoder was added as the final architecture.

Each of the 39 numerical features is treated as a token. The features are projected into a learned embedding space and combined with trainable positional embeddings.

Transformer configuration:

- Embedding dimension (`d_model`): **64**
- Attention heads: **4**
- Feed-forward dimension: **128**
- Transformer encoder blocks: **2**
- Attention/encoder dropout: **0.20**
- Classification-layer dropout: **0.30**
- Global Average Pooling
- Dense(64, ReLU)
- Dense(8, Softmax)

The Transformer therefore provides a self-attention-based alternative to the feed-forward, convolutional, and recurrent models.

---

## 6. Training Configuration

The common training configuration used in the notebook is:

| Hyperparameter | Value |
|---|---|
| Optimizer | Adam |
| Loss | Sparse Categorical Crossentropy |
| Maximum epochs | 20 |
| Batch size | 256 |
| Number of classes | 8 |
| Input features | 39 |
| Random state | 42 |
| Early stopping | Used for Proposed DNN, CNN, LSTM and Transformer |
| Learning-rate reduction | ReduceLROnPlateau used for Proposed DNN, CNN, LSTM and Transformer |

The notebook reports **TensorFlow 2.21.0**. Exact versions of the other installed packages were not explicitly recorded in the notebook.

---

## 7. Hyperparameter Tuning / Model Variations

The project includes controlled model variations rather than a large automated hyperparameter search.

The main comparisons are:

- Baseline DNN
- DNN + Dropout
- DNN + L2
- Proposed DNN combining Dropout + L2 + Batch Normalization
- 1D CNN
- LSTM
- Transformer

The regularization parameters explicitly specified in the notebook include:

- Dropout: **0.30** and **0.20** depending on layer
- L2 regularization: **0.001**
- Transformer dropout: **0.20**
- Transformer attention heads: **4**
- Transformer embedding dimension: **64**
- Transformer feed-forward dimension: **128**

Early stopping and learning-rate reduction are used for the proposed DNN, CNN, LSTM and Transformer models.

---

## 8. Evaluation Metrics

The models are evaluated on the original test set using:

### Accuracy

Measures the proportion of correctly classified samples.

### Macro Precision

Computes precision independently for each class and then averages the class-level values.

### Macro Recall

Measures the average ability of the model to identify samples from each class.

### Macro F1 Score

Provides a balanced measure of precision and recall across all classes.

### Macro Specificity

Measures the average true-negative rate across the eight classes.

### Training Time

Measures the time required to train each model and is used to study the performance-versus-computational-cost trade-off.

---

## 9. Experimental Results

The final comparison evaluates all seven architectures using the same test set and metrics.

The final result values should be taken from:

```text
result/CICIoT2023_DL_Model_Comparison.csv
```

The project results show that:

- **1D CNN** provides the strongest overall predictive performance in the final experiment.
- **DNN** provides competitive predictive performance with substantially lower training cost than the more complex architectures.
- **DNN + L2** is the most training-efficient among the reported models.
- **Transformer** provides a useful attention-based comparison, but its training cost is substantially higher than the simpler DNN variants.
- Model selection therefore depends not only on predictive performance but also on computational cost.

### Important Reported Result

The final experiment reports:

**1D CNN**
- Accuracy: **75.74%**
- Macro F1: **63.77%**

**Transformer**
- Accuracy: **63.90%**
- Macro F1: **51.97%**
- Training time: **889.91 seconds**

These values should be cross-checked against the generated CSV result file included in the `result/` folder.

---

## 10. Innovation

The main contribution of this project is a **controlled comparative study of multiple deep learning architectures for multiclass IoT intrusion detection** under a common preprocessing and evaluation framework.

The project goes beyond a single baseline by comparing:

- Feed-forward learning
- Regularization through Dropout
- Regularization through L2
- Combined regularization with Batch Normalization
- Convolutional learning
- Recurrent learning
- Self-attention through a Transformer encoder

The addition of the lightweight Transformer extends the original comparison and investigates whether self-attention can effectively model relationships among tabular network-flow features.

The project also explicitly evaluates **training cost**, rather than reporting predictive accuracy alone.

---

## 11. State-of-the-Art Comparison

The project documentation contains literature and state-of-the-art analysis for CICIoT2023-based intrusion detection.

When comparing against published studies, the following must be considered:

- Number of classes may differ.
- Binary, multiclass, and fine-grained attack classification tasks are not equivalent.
- Sampling and class-balancing strategies may differ.
- Train/test splits may differ.
- Metrics may be calculated differently.

Therefore, published accuracy values should not be interpreted as directly comparable unless the experimental settings are sufficiently aligned.

The detailed literature and SOTA comparison are stored in the `documentation/` folder.

---

## 12. Results and Figures

The `figures/` directory contains project visualizations such as:

- Class distribution
- Model accuracy comparison
- Macro F1 comparison
- Training-time comparison
- Training/validation curves
- Confusion matrices

The `result/` directory contains generated numerical results, including the model comparison CSV.

---

## 13. Project Folder Structure

The project is organized as follows:

```text
deep learning project/
│
├── README.md
│
├── data information/
│   ├── dataset information
│   └── dataset-related documentation
│
├── documentation/
│   ├── literature review
│   ├── SOTA analysis
│   └── project documentation
│
├── figures/
│   ├── class distribution
│   ├── model comparison
│   ├── training curves
│   ├── confusion matrices
│   └── other project figures
│
├── presentation/
│   └── project PowerPoint presentation
│
├── report/
│   └── final project report
│
├── result/
│   ├── CICIoT2023_DL_Model_Comparison.csv
│   └── other result files
│
└── source code/
    └── DeepLearningFinal_Transformer_FINAL(3).ipynb
```

---

## 14. Reproducibility

To reproduce the experiment:

1. Install Python and the required packages.
2. Place the CICIoT2023 Parquet files in the working directory.
3. Open the notebook in Jupyter Notebook, JupyterLab, or Google Colab.
4. Run the cells sequentially.
5. Ensure the dataset files are named:
   - `train-00000-of-00001.parquet`
   - `test-00000-of-00001.parquet`
6. Run preprocessing and balancing.
7. Train the seven models.
8. Evaluate the predictions on the test set.
9. Generate the confusion matrices and comparison plots.
10. Save the final comparison CSV.

### Software

The notebook explicitly reports:

```text
TensorFlow 2.21.0
```

The notebook installs/uses:

```text
pyarrow
pandas
numpy
scikit-learn
matplotlib
seaborn
tensorflow
```

---

## 15. Limitations

1. The balanced training experiment uses a maximum of 10,000 samples per class, so it does not use the entire original training distribution.
2. The original CICIoT2023 test set remains imbalanced, which can influence aggregate metrics.
3. The experiment uses tabular flow features rather than raw packet sequences.
4. The Transformer treats individual numerical features as tokens; this is an experimental tabular representation rather than a conventional NLP Transformer.
5. Training time depends on the available computing environment.
6. The notebook reports no GPU available in the recorded execution environment.
7. Published SOTA results may use different class definitions, splits and preprocessing, limiting direct comparison.
8. The current experiment does not establish deployment-level real-time performance.

---

## 16. Future Scope

Possible future improvements include:

- Hyperparameter optimization using systematic search methods.
- More extensive ablation studies.
- Feature selection and dimensionality reduction.
- Class-specific error analysis.
- Ensemble or hybrid CNN–Transformer architectures.
- Efficient Transformer variants for lower training cost.
- Evaluation on additional CICIoT2023 splits.
- Real-time intrusion detection benchmarking.
- Model compression and deployment on resource-constrained IoT devices.
- Explainable AI methods such as SHAP or feature attribution.
- Robustness testing against changing or previously unseen attack patterns.

---

## 17. Conclusion

This project provides a comparative deep learning study for multiclass IoT intrusion detection using CICIoT2023.

Seven architectures were evaluated under a common preprocessing and evaluation framework. The results demonstrate that increasing architectural complexity does not automatically guarantee better performance. In the final experiment, the **1D CNN achieved the strongest predictive performance**, while simpler DNN variants offered attractive computational efficiency.

The addition of the Transformer provides an attention-based perspective and extends the project beyond conventional feed-forward, convolutional, and recurrent architectures. The comparison highlights the importance of evaluating both **classification quality and computational cost** when selecting a model for practical IoT intrusion detection.

---

## 18. Key Files

| File / Folder | Purpose |
|---|---|
| `source code/DeepLearningFinal_Transformer_FINAL(3).ipynb` | Main experiment notebook |
| `result/CICIoT2023_DL_Model_Comparison.csv` | Final model comparison results |
| `figures/` | Generated graphs and evaluation figures |
| `documentation/` | Literature review and SOTA analysis |
| `report/` | Project report |
| `presentation/` | Project presentation |
| `data information/` | Dataset-related information |

---

## 19. Submission Checklist

Before submission, verify that the project contains:

- [x] README.md
- [ ] requirements.txt
- [x] Source code / notebook
- [x] Dataset information
- [x] Model information
- [x] Hyperparameters
- [x] Model comparison
- [x] Training results
- [x] Test results
- [x] Confusion matrices
- [x] Performance metrics
- [x] Literature review
- [x] SOTA comparison
- [x] Figures and tables
- [x] Project report
- [x] Project presentation

---

## 20. Author

**Namrata Kothari**  
**Registration Number:** 2430010345  
**Section:** D  
**Manipal University Jaipur**
