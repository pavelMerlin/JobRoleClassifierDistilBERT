# Multi-label Classification with DistilBERT

This project performs multi-label classification of job titles, predicting labels such as job levels, roles, and responsibilities. It uses a pre-trained DistilBERT model, fine-tuned for the task of predicting multiple labels for each job title.

[Link to a video with a small explanation](https://youtu.be/RUZ-wqrUY2w)

## Table of Contents

- [Overview](#overview)
- [Requirements](#requirements)
- [Data Preprocessing](#data-preprocessing)
- [Model](#model)
- [Training and Cross-validation](#training-and-cross-validation)
- [Experiments with class imbalance](#experiments-with-class-imbalance)
- [Evaluation](#evaluation)
- [Saving and Loading the Model](#saving-and-loading-the-model)
- [Results](#results)

## Overview

This project uses the lightweight transformer model **DistilBERT** for multi-label classification. The goal is to assign multiple labels to job titles based on their content. Each job title can belong to one or more classes, such as "Manager," "Individual Contributor," or "Director."

### Why DistilBERT?

- **Efficiency**: DistilBERT is a smaller and faster version of BERT that retains most of its performance.
- **Multi-label Classification**: DistilBERT is capable of handling multi-label tasks, making it suitable for predicting multiple job title categories.

## Requirements

To install the required dependencies for this project, you can use the `requirements.txt` file. Run the following command:

```
pip install -r requirements.txt
```

## Running the Model

The file **analysis_distilBERT_multilabel.ipynb** contains a description of the decisions made and an analysis of the data. By studying this file you can understand the course of decision making. 

The .py files with model_train and generation_from_the_model can be added to the git hub repository if needed.

## Data Preprocessing

The input data consists of a CSV file (`JobLevelData.csv`) 

1. **Label Merging**: Labels from multiple columns are merged into a single list of labels for each job title.
2. **Missing Labels**: Rows with missing labels are filled with a default label (`"Not Required"`).
3. **Tokenization**: Each job title is tokenized using the **DistilBERT tokenizer**, converting job titles into token IDs.

## Model

The pre-trained **DistilBERT** model is loaded using Huggingface's `transformers`. The model architecture is adapted for multi-label classification by using:

- **BCEWithLogitsLoss**: Combines sigmoid activation and binary cross-entropy loss, ideal for multi-label tasks.
- **AdamW** optimizer: Optimized for controlling weight decay during training.

## Training and Cross-validation

The model is trained using **5-fold Stratified Cross-validation** to ensure even distribution of labels across folds. Each fold is trained with:

- **Batch size**: 4
- **Number of epochs**: 3

For each fold, data is tokenized, padded to a uniform length, and passed through the model for training and validation.

- The solution to class imbalance is assigning higher weights to the underrepresented classes.

## Experiments with Class Imbalance

### 1. Focal Loss for Class Imbalance
- Focal Loss was used to handle class imbalance but failed to fully address the issue. The observations were:
  1. **Errors remained** primarily in the underrepresented classes.
  2. **Focal Loss performed worse** than `BCEWithLogitsLoss`.

**Results**:
- **F1-score on TEST set**: 0.989
- **Accuracy on TEST set**: 0.981
- **Wrong predictions**: 26 out of 2240

### 2. Using `WeightedRandomSampler`
- The `WeightedRandomSampler` was applied to oversample the underrepresented classes. The following issues were observed:
  1. The model **no longer made errors** on underrepresented classes.
  2. However, it **increased errors overall**.

**Results**:
- **F1-score on TEST set**: 0.986
- **Accuracy on TEST set**: 0.982
- **Wrong predictions**: 31 out of 2240

### 3. Using `SMOTEENN`
- `SMOTEENN` does not support multi-label tasks due to its limitation of only handling binary, multiclass, or single-encoded multiclass target variables.

## Evaluation

To evaluate the model, the following metrics are used:

- **F1-score (micro)**: Measures the harmonic mean of precision and recall, important for handling imbalanced data.
- **Accuracy**: Indicates the proportion of correctly predicted labels.

Both metrics are computed on the training and test sets for each fold.

## Saving and Loading the Model

After training, the model and tokenizer are saved for future use in file **distilBERT_model**
These can be loaded later for making predictions on new job titles.

## Results

The decisions made showed high accuracy on the test dataset. 

Results at the end of training:

F1-score: 0.99
Accuracy: 0.99
