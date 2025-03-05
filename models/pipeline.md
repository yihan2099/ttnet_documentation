# AutoML Pipeline Overview

This page provides information about the AutoML pipeline in TTNet, which automates the process of model development, evaluation, and deployment.

## Pipeline Sections

The AutoML pipeline is divided into multiple interconnected components:

1. **Dataset Processing Pipeline**
2. **Feature Engineering Pipeline**
3. **Model Training and Evaluation Pipeline**
4. **Rule Generation and Model Deployment Pipeline**

Below is a detailed explanation of each component in the AutoML pipeline.

---

## 1. Dataset Processing Pipeline

The dataset processing pipeline is responsible for preparing the raw data for model training. It includes several important stages:

1. **User Pre-Cleans Datasets**:
   - Users manually pre-clean the datasets to ensure initial quality and to reduce any major issues that might impede further automated processing.

2. **User Upload CSV File with Header**:
   - Users upload a cleaned CSV file containing the data to be processed.

3. **Data Analyzing**:
   - The system analyzes the uploaded data, identifying missing values, data types, and other characteristics to understand the dataset's structure.

4. **Generating Metadata**:
   - Metadata is automatically generated from the data analysis step. This metadata includes details about the data types, missing values, potential outliers, and suggested encoding strategies.

5. **User Editing Metadata**:
   - Users have the option to manually adjust or confirm the generated metadata to ensure accuracy and provide any additional information required for downstream processing.

6. **Generate Processed Data**:
   - Based on the metadata, the system processes the data, including operations like data cleaning, column dropping, and feature encoding.

### Data Analysis Pipeline

The data analysis pipeline includes data type classification and drop analysis based on predefined rules.

### Data Type Determination Rules

- **Continuous Features:**
  - **CONTINUOUS.FLOAT:** Float numeric data.
  - **CONTINUOUS.INTEGER:** Integer numeric data.

- **Categorical Features:**
  - **CATEGORICAL.NOMINAL:** Nominal categorical data.
  - **CATEGORICAL.ORDINAL:** Ordinal categorical data.
  - **CATEGORICAL.BINARY:** Binary categorical data.

- **Temporal Features:**
  - **TEMPORAL.DATETIME:** Datetime data type.
  - **TEMPORAL.DURATION:** Temporal duration data.
  - **TEMPORAL.CYCLIC:** Cyclic temporal data like days of the week.

### Drop Analysis Rules

Columns are dropped based on the following conditions:

- **High Missing Rate:** Columns with missing values above a threshold (e.g., 90%).
- **Constant Values:** Columns where a single value dominates (e.g., appears more than 95% of the time).
- **High Correlation:** Columns highly correlated with others (correlation above 0.95).
- **High Variance Inflation Factor (VIF):** Columns with VIF above a threshold (e.g., 5.0).
- **Zero Variance:** Columns with zero variance.
- **Unique Identifiers:** Columns that are likely unique identifiers (unique ratio above 95%).
- **High Cardinality:** Categorical features with a high number of unique values (e.g., over 100 unique values).
- **Duplicate Columns:** Columns that are duplicates of others.
- **High Skewness or Kurtosis:** Numeric columns with skewness or kurtosis above thresholds (e.g., 10).

## 2. Feature Engineering Pipeline

The feature engineering pipeline refines features for optimal model performance:

1. **Metadata Input**:
   - The pipeline starts with the metadata generated during the dataset processing stage, which informs feature analysis and selection.

2. **Feature Analysis**:
   - Analyzes features to determine relationships, redundancy, and potential improvements. The analysis includes identifying categorical and numerical variables that require specific treatment.

3. **Feature Encoding**:
   - Encodes categorical features using appropriate techniques such as one-hot encoding or label encoding, informed by the metadata and feature analysis.

### Encoding Methods and Rules

- **Categorical Features:**
  - **Binary Encoding:** Used for features with exactly 2 unique values.
  - **One-Hot Encoding:** Applied to nominal features with low cardinality (≤ 10 unique values).
  - **Label Encoding:** Used for ordinal features with low cardinality.
  - **Frequency Encoding:** For features with high cardinality (> 10 unique values).
  - **Target Encoding:** Applied when necessary based on analysis.

- **Continuous Features:**
  - **Standard Scaling:** Default for continuous features.
  - **Robust Scaling:** Applied to skewed distributions.
  - **Min-Max Scaling:** Used when features need to be scaled to a specific range.
  - **Binning:** For features with high unique counts.

**Model-Specific Encoding Defaults:**

- **Random Forest and XGBoost:**
  - Categorical Features: Label Encoding.
  - Numeric Features: Standard Scaling.

- **TTNet:**
  - Categorical Features: One-Hot Encoding.
  - Numeric Features: Standard Scaling.

**Feature Engineering**:
   - Creates new features from the existing dataset to enhance model performance, including transformations, aggregations, and interaction features.

**Data Splitting**:
   - Splits the processed data into training, validation, and test sets in preparation for modeling.

## 3. Model Training and Evaluation Pipeline

The core model training process of the AutoML pipeline includes:

1. **Preload TTNet Model**:
   - Preloads the TTNet model with its necessary configurations to be used in the training process.

2. **Grid Search**:
   - Executes a grid search to explore hyperparameter combinations and identify the optimal configuration for the model. The user can also input parameters that override the default grid search settings.

3. **MLflow Tracking**:
   - Logs model metrics and parameters, and tracks the training process to select the best model configuration.

4. **Choose the Best Model**:
   - The best-performing model is selected based on logged metrics and is ready for further processing.

### Model Selection

The following models are trained and evaluated:

- **Random Forest Classifier**
  - An ensemble learning method that constructs multiple decision trees.
- **XGBoost Classifier**
  - An optimized gradient boosting library designed for speed and performance.
- **TTNet (Tabular XAI Model)**
  - A neural network model designed for tabular data.

Each model is trained using the specified encoding strategies and evaluated based on accuracy and other metrics.

## 4. Rule Generation and Model Deployment Pipeline

The final part of the AutoML pipeline involves rule extraction and model deployment:

1. **Model Registry**:
   - The best model is stored in the MLflow model registry for versioning and future deployment.

2. **Rule Generation**:
   - Extracts interpretable rules from the trained TTNet model using the generated metadata and encoder information.

3. **Push Rules into PostgreSQL**:
   - Stores the generated rules in a PostgreSQL database, associating each rule with the corresponding model ID for easy reference and future inference.

## Pipeline Overview

The **AutoML Pipeline** for TTNet comprises several interconnected sub-pipelines that streamline the process of data preprocessing, feature engineering, model training, and rule generation. Each stage in the pipeline has been carefully designed to support automation, reproducibility, and interpretability, leveraging MLflow for experiment tracking and model management. This end-to-end automation allows users to efficiently generate interpretable models with minimal manual intervention while ensuring a reliable and repeatable workflow. 