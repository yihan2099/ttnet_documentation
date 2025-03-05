---
icon: bullseye-arrow
---

# Quickstart

This guide provides a quick introduction to getting started with TTNet.

## Installation

Install TTNet using pip:

```bash
pip install ttnet
```

For other installation options, see the [installation guide](installation.md).

## Basic Usage

### 1. Initialize TTNet

```python
from ttnet import TTNet

# Initialize TTNet
ttnet = TTNet()
```

### 2. Working with Datasets

```python
# Create a new dataset
dataset = ttnet.datasets.create("my_dataset")

# Upload data to the dataset
dataset.upload("path/to/data.csv")

# Analyze data quality
quality_report = dataset.analyze_quality()
print(quality_report)
```

### 3. Working with Models

```python
# Create a new model
model = ttnet.models.create("my_model")

# Train the model
training_job = model.train(
    dataset=dataset,
    hyperparameters={
        "learning_rate": 0.01,
        "epochs": 10
    }
)

# Monitor training progress
training_job.monitor()

# Deploy the model
deployment = model.deploy(
    name="my-model-api",
    deployment_type="api"
)
```

### 4. Working with Rules

```python
# Create a new rule
rule = ttnet.rules.create(
    name="my_rule",
    description="A sample rule",
    rule_code="value > 0"
)

# Validate the rule
validation_result = rule.validate(dataset=dataset)
print(validation_result)

# Apply the rule
application_result = rule.apply(dataset=dataset)
print(application_result)
```

## Next Steps

- Learn more about [Dataset Management](../datasets/data-management.md)
- Explore [Model Training](../models/model-training.md)
- Understand [Rule Validation](../rules/rule-validation.md)
- Check out the [TTNet MLflow integration](../ttnet-mlflow/index.md)
