# Models Overview

This section provides information about machine learning models and the AutoML pipeline in TTNet.

## What is Model Management?

Model management in TTNet encompasses the key aspects of machine learning model lifecycle:

- **Model Development**: Creating and iterating on model architectures
- **Model Versioning**: Tracking changes to models over time
- **Model Evaluation**: Assessing model performance
- **Model Registry**: Storing and organizing models in a central repository
- **Rule Extraction**: Generating interpretable rules from trained models

## AutoML Pipeline

TTNet features an end-to-end AutoML pipeline that automates the model development process:

1. **Dataset Processing**: Preparing and analyzing data for model training
2. **Feature Engineering**: Transforming and encoding features for optimal model performance
3. **Model Training and Evaluation**: Training multiple models and selecting the best performer
4. **Rule Generation**: Extracting interpretable rules from the trained models

## Key Model Features

TTNet offers several features for comprehensive model management:

1. **Experiment Tracking**: Track all training runs and parameters
2. **Version Control**: Maintain model versioning and lineage
3. **Performance Metrics**: Standard and custom metrics for evaluation
4. **Rule Generation**: Extract interpretable rules from trained models
5. **Integration with MLflow**: Enhanced experiment tracking capabilities

## Supported Models

TTNet supports multiple model types:

- **Random Forest Classifier**: An ensemble learning method that constructs multiple decision trees
- **XGBoost Classifier**: An optimized gradient boosting library designed for speed and performance
- **TTNet Model**: A neural network model designed specifically for tabular data with explainability features

## Getting Started with Models

To learn more about the AutoML pipeline in TTNet, check out:

- [Pipeline](pipeline.md): End-to-end AutoML pipeline for model development 