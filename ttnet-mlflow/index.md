# ttnet

<a target="_blank" href="https://cookiecutter-data-science.drivendata.org/">
    <img src="https://img.shields.io/badge/CCDS-Project%20template-328F97?logo=cookiecutter" />
</a>

TTNet refactored under MLflow framework

## Local Development Setup

1. Create a Conda environment from file

    use conda 24.7.1
```
conda env create -f requirements/environment.yml
conda env update -f requirements/environment.yml
```
2. Activate the Conda environment
```
conda activate ttnet-mlflow
```
3. Start the MLflow UI
```
mlflow ui --port 5000
```
4. Clean up the Conda environment
```
conda deactivate
conda remove --name <env_name> --all
```
5. Clean up mlflow server
```
# Kill all processes containing "mlflow"
pkill -f mlflow

# If they don't respond to normal kill, force kill
pkill -9 -f mlflow
```

## Support for Docker Compose

Follow these steps to set up and manage your local environment:

1. **Configuration**

    Configure your dockerfile to your local machine: choose x86 or arm64.

2. **Build the Stack**

   First, build your Docker stack with the following command:

        $ docker compose -f local.yml build

3. **Run the Stack**
    Then, run the stack with the following command:

        $ docker compose -f local.yml up

4. **Restart the Stack**
    If you need to restart the stack, use the following command:

        $ docker compose -f local.yml restart

## Development with Dev Containers

This project supports development using VS Code Dev Containers, which provides a consistent, isolated development environment.

### Prerequisites

1. [VS Code](https://code.visualstudio.com/)
2. [Docker](https://www.docker.com/)
3. [Dev Containers extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers)
4. [NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html) (for GPU support)

### Getting Started with Dev Container

1. Clone the repository and open it in VS Code:
    ```bash
    git clone <repository-url>
    code ttnet_mlflow
    ```

2. When prompted "Reopen in Container", click "Reopen in Container". Alternatively:
   - Press F1, select "Dev Containers: Reopen in Container"

3. Wait for the container to build and start. The first build may take several minutes.

### Features

The dev container provides:
- Pre-configured Python environment with conda
- CUDA support for GPU development
- Git configuration
- VS Code extensions for Python development
- Consistent environment across team members

### Customization

- Additional VS Code settings: `.devcontainer/devcontainer.json`
- Container configuration: `compose/local/django/celery/worker/Dockerfile`
- Environment setup: `compose/local/django/celery/worker/start`

## Delta Lake Integration

The project includes Delta Lake support for reliable data storage and processing:

1. **Access Delta Lake**

   After starting the stack, Delta Lake is available at:
   - Spark Shell: `http://localhost:8080`

2. **Configure Delta Lake Storage**

   Delta Lake is configured to work with MinIO as the storage backend:
   - Storage URL: `http://minio:9000`
   - Configure access credentials in `.envs/.local/.deltalake`

3. **Using Delta Lake**

   Connect to the Spark shell and create Delta tables:
   ```python
   from delta.tables import *

   # Create a Delta table
   data.write.format("delta").save("/path/to/delta-table")
   ```

## AutoML Pipeline Overview

This project implements an AutoML pipeline for the TTNet (Truth Table Network) model using MLflow for experiment tracking and model management. The pipeline consists of several key stages designed to automate the process of model development, evaluation, and deployment:

1. **Dataset Processing Pipeline**
2. **Feature Engineering Pipeline**
3. **Model Training and Evaluation Pipeline**
4. **Rule Generation and Model Deployment Pipeline**

## Key Features

- Automated ML workflow with MLflow integration
- Experiment tracking and parameter logging
- Model versioning and artifact storage
- Performance metrics logging and visualization
- Rule extraction for model interpretability

## CI/CD Pipeline

This project uses GitHub Actions for continuous integration and deployment.

### Continuous Integration
- Automated testing on push and pull requests
- Code quality checks with Ruff
- Test coverage reporting with pytest-cov
- Coverage visualization with Codecov

### Running Tests Locally
```bash
# Run tests with coverage
pytest --cov

# Run linting
ruff check .
ruff format .
```

## Development Setup

### Local Development

1. Create and activate the conda environment:
```bash
conda env create -f requirements/environment.yml
conda activate ttnet
```

2. Install package in development mode:
```bash
pip install -e ".[dev]"
```

3. Install pre-commit hooks:
```bash
pre-commit install
```

### Using Dev Container

1. Open in VS Code:
```bash
code ttnet_mlflow
```

2. When prompted, click "Reopen in Container"

The dev container will automatically:
- Set up the conda environment
- Install the package in development mode
- Install pre-commit hooks
- Configure PYTHONPATH

### Testing Changes

Your changes to the source code in `src/ttnet` will be immediately reflected without reinstallation:

```bash
# Run tests
pytest

# Import your package
python
>>> import ttnet
>>> ttnet.__version__
'0.0.1'
```

### Code Quality Tools

This project uses several tools to maintain code quality:

```bash
# Install development dependencies
conda env create -f environment.yml

```bash
# Install and configure pre-commit hooks
./scripts/setup-dev.sh

# Run manually on all files
pre-commit run --all-files
```

### Pre-commit Hooks

Pre-commit hooks will automatically check your code before committing:
- Code formatting (ruff, black)
- Import sorting (isort)
- Type checking (mypy)
- Unit tests (pytest)
- Security checks
- File formatting

### Continuous Integration

Our CI pipeline runs the following checks:
- Code quality (ruff, black, isort)
- Type checking (mypy)
- Unit tests with coverage
- Security scanning

All checks must pass before merging pull requests.

## Project Organization

```
├── LICENSE            <- Open-source license if one is chosen
├── Makefile           <- Makefile with convenience commands like `make data` or `make train`
├── README.md          <- The top-level README for developers using this project.
├── data
│   ├── external       <- Data from third party sources.
│   ├── interim        <- Intermediate data that has been transformed.
│   ├── processed      <- The final, canonical data sets for modeling.
│   └── raw            <- The original, immutable data dump.
│
├── docs               <- A default mkdocs project; see www.mkdocs.org for details
│
├── models
│   ├── automl         <- Models prepared for the automl pipeline on platform, organized by task, including model and config
│   ├── rule2model     <- Models used for rule2model pipeline
├── clients            <- Client code who we work directly with not through the platform
│   ├── models         <- Models specific designed for the client
│   ├── data           <- Clients data stored in local development, DO NOT COMMIT TO GIT
│   ├── config         <- Config files for the client
│
├── notebooks          <- Jupyter notebooks. Naming convention is a number (for ordering),
│                         the creator's initials, and a short `-` delimited description, e.g.
│                         `1.0-jqp-initial-data-exploration`.
│
├── pyproject.toml     <- Project configuration file with package metadata for
│                         ttnet and configuration for tools like black
│
├── references         <- Data dictionaries, manuals, and all other explanatory materials.
│
├── reports            <- Generated analysis as HTML, PDF, LaTeX, etc.
│   └── figures        <- Generated graphics and figures to be used in reporting
│
├── requirements.txt   <- The requirements file for reproducing the analysis environment, e.g.
│                         generated with `pip freeze > requirements.txt`
│
├── setup.cfg          <- Configuration file for flake8
│
└── ttnet   <- Source code for use in this project.
    │
    ├── __init__.py             <- Makes ttnet a Python module
    │
    ├── config.py               <- Store useful variables and configuration
    │
    ├── dataset.py              <- Scripts to download or generate data
    │
    ├── features.py             <- Code to create features for modeling
    │
    ├── modeling
    │   ├── __init__.py
    │   ├── predict.py          <- Code to run model inference with trained models
    │   └── train.py            <- Code to train models
    │
    └── plots.py                <- Code to create visualizations

--------
