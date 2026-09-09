Wearable HRV-Based ADHD Screening Using Hybrid Deep Learning

This repository contains the implementation associated with the manuscript:

"An Informatics Framework for Wearable HRV-Based ADHD Screening Using Hybrid Deep Learning"

The code implements a wearable heart rate variability (HRV)-based framework for ADHD screening using a hybrid CNN-BiLSTM-Attention deep learning model. The framework combines automatically learned representations from raw HRV signals with handcrafted HRV features extracted using tsfresh.

Repository Contents
Preprocessing-HRV-Handcrafted-Features-Extractions.ipynb
Preprocesses the raw HRV data, performs fixed-length segmentation, extracts handcrafted HRV features using tsfresh, selects relevant features, normalizes the features, and saves the resulting feature data.
Hrv-CNN-BiLSTM-Attention-Model.ipynb
Loads the preprocessed HRV segments and handcrafted features, trains the hybrid CNN-BiLSTM-Attention model, evaluates its performance, and performs permutation feature importance (PFI) analysis.
requirements.txt
Lists the Python packages required to run the notebooks.
Dataset

The experiments use the publicly available Hyperaktiv dataset:

https://osf.io/3agwr

The dataset is not redistributed in this repository. Users should obtain the dataset from its original source and place the extracted files in the directory structure expected by the notebooks.

The dataset contains long-duration HRV recordings collected using the Actiheart chest-worn ECG-based monitoring device. The study uses data from 80 participants, including 38 participants with ADHD and 42 clinical controls.

Experimental Workflow

The implementation consists of two main stages.

1. HRV preprocessing and handcrafted feature extraction

Preprocessing-HRV-Handcrafted-Features-Extractions.ipynb performs the following operations:

Loads the raw HRV/IBI recordings.
Removes initial unexpected values from the recordings.
Handles the Actiheart artifact represented by repeated IBI values of 2000.
Interpolates missing HRV values.
Segments each HRV recording into fixed-length sequences.
Uses a segment length of 1024 HRV values with 50% overlap between consecutive segments.
Extracts statistical HRV features using the tsfresh package.
Initially evaluates 788 candidate features.
Selects statistically relevant features using a p-value threshold of p < 0.05.
Retains 28 engineered HRV features.
Standardizes the engineered features.
Saves the resulting segment-level feature data for use by the deep learning model.

For the 1024-sample configuration, the resulting dataset contains approximately 15,002 HRV segments, with 28 engineered features per segment.

2. Hybrid CNN-BiLSTM-Attention Model

Hrv-CNN-BiLSTM-Attention-Model.ipynb implements the hybrid deep learning architecture.

The model has two parallel inputs:

Raw HRV signal branch: fixed-length HRV sequences are processed using convolutional layers, bidirectional LSTM layers, and an attention mechanism.
Engineered feature branch: the 28 selected handcrafted HRV features are provided as an additional input.
Raw HRV signal branch

The CNN component consists of multiple 1D convolutional blocks with:

Conv1D layers
LeakyReLU activation
Batch normalization
Max pooling
Dropout
Residual connections

The resulting representations are processed by two bidirectional LSTM layers. A residual connection is included after the first BiLSTM layer, followed by an attention mechanism and global average pooling.

Feature fusion and classification

The pooled deep-learning representation is concatenated with the 28 engineered HRV features. The combined representation is passed through a fully connected layer with LeakyReLU activation and dropout, followed by a sigmoid output for binary classification.

The model is trained using:

Binary cross-entropy loss
Adam optimizer
Initial learning rate of 0.001
Batch size of 64
Early stopping
Learning-rate reduction on plateau
Model Evaluation

The implementation uses an 80% training / 20% testing split. Stratified 10-fold cross-validation is performed within the training data, and the resulting models are evaluated on the held-out test set.

The primary evaluation metrics are:

Accuracy
Precision
Recall
F1-score
Matthews Correlation Coefficient (MCC)

For the 1024-sample configuration, the hybrid model achieved:

Metric	Performance
Accuracy	0.76
Precision	0.76
Recall	0.74
F1-score	0.75
MCC	0.52



These results correspond to the experiments reported in the manuscript.

Model Interpretability

The model implementation includes permutation feature importance (PFI) analysis for the 28 engineered HRV features.

For each engineered feature, its values in the test set are randomly permuted while all other inputs remain unchanged. The decrease in classification accuracy is used as the feature's importance score.

PFI is applied specifically to the 28 engineered HRV features. It does not directly visualize or quantify the temporal attention weights assigned to individual time points in the raw HRV sequences.

Reproducing the Experiments
1. Clone the repository
git clone <ANONYMOUS_REPOSITORY_URL>
cd <REPOSITORY_DIRECTORY>
2. Install the required Python packages
pip install -r requirements.txt
3. Obtain the Hyperaktiv dataset

Download the publicly available dataset from:

https://osf.io/3agwr

Do not redistribute the dataset through this repository.

4. Configure the dataset paths

The notebooks currently expect the Hyperaktiv dataset to be available under a directory structure similar to:

hyperaktiv/
├── patient_info.csv
├── features.csv
└── hrv_data/
    ├── ...
    └── ...

The notebooks use paths such as:

/kaggle/input/hyperaktiv/hyperaktiv/

when executed in a Kaggle environment.

If running locally, update the dataset paths in the notebooks accordingly.

5. Run preprocessing

Open:

Preprocessing-HRV-Handcrafted-Features-Extractions.ipynb

Run the notebook to preprocess the HRV recordings, segment the signals, extract the selected handcrafted features, normalize them, and save the segment-level feature files.

6. Run the hybrid model

Open:

Hrv-CNN-BiLSTM-Attention-Model.ipynb

Run the notebook after completing the preprocessing stage.

The notebook loads the HRV segments and the corresponding 28-dimensional engineered feature vectors, trains the hybrid model, evaluates the model, generates ROC/PR curves, and performs permutation feature importance analysis.

Important Notes
The dataset is not included in this repository because it is available from the original public data source.
The repository contains implementation code and does not provide a clinical diagnostic system.
The study was developed and evaluated using adult participants. Pediatric validation is required before considering real-world clinical deployment.
Results may vary slightly because neural-network training involves stochastic optimization.
The repository is intended to support methodological transparency and reproducibility of the experiments reported in the associated manuscript.
Citation

If you use this code, please cite the associated manuscript:

An Informatics Framework for Wearable HRV-Based ADHD Screening Using Hybrid Deep Learning.

License

This repository is provided for research and reproducibility purposes. Please check the licensing terms of the Hyperaktiv dataset separately before using or redistributing the dataset.
License

This repository is provided for research and reproducibility purposes. Please check the licensing terms of the Hyperaktiv dataset separately before using or redistributing the dataset.
