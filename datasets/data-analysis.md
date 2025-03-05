# Data Analysis

## Overview

The data analysis module provides comprehensive functionality for analyzing datasets through the `DataAnalyzer` class. This documentation details the analysis process, including feature type detection, metadata generation, and column analysis for potential dropping.

## Feature Type Analysis

### Supported Data Types

The analyzer categorizes features into three main groups:

1. **Continuous Data**
   - **Float**: Decimal numbers (e.g., prices, measurements)
   - **Integer**: Whole numbers (e.g., counts)

2. **Categorical Data**
   - **Binary**: Two distinct values (e.g., yes/no, true/false)
   - **Nominal**: Unordered categories
   - **Ordinal**: Ordered categories (e.g., ratings, levels)

3. **Temporal Data**
   - **Datetime**: Timestamps and dates
   - **Duration**: Time periods and intervals
   - **Cyclic**: Repeating time patterns (e.g., days of week, months)

### Feature Type Detection Process

The analyzer follows a sophisticated decision tree to determine feature types:

1. **Initial Data Preparation**
   ```python
   # Handle missing data and basic statistics
   total_count = len(series)
   missing_count = series.isnull().sum()
   non_missing_series = series.dropna()
   feature_info["statistics"] = {
       "total_count": total_count,
       "missing_count": missing_count,
       "missing_rate": missing_count / total_count if total_count > 0 else 0,
       "unique_count": non_missing_series.nunique(),
   }
   ```

2. **Empty Series Detection**
   ```python
   if total_count == 0 or non_missing_series.empty:
       feature_info["type"] = "TO_BE_REMOVED"
       feature_info["warning"] = True
       feature_info["feature_type_reason"] = "Empty series"
   ```

3. **Unique Value Analysis**
   ```python
   unique_count = non_missing_series.nunique()
   unique_ratio = unique_count / non_missing_count

   if unique_count == non_missing_count:
       if is_integer_like(converted_series):
           feature_info["type"] = "CATEGORICAL.NOMINAL"
           feature_info["feature_type_reason"] = "All values are unique. Might be CONTINUOUS or represent an ID."
       else:
           feature_info["type"] = "CONTINUOUS.FLOAT"
           feature_info["feature_type_reason"] = "All values are unique and numeric."
   ```

4. **Numeric Type Analysis**
   ```python
   if np.issubdtype(converted_series.dtype, np.number) and unique_ratio > unique_ratio_threshold:
       if is_integer_like(converted_series):
           feature_info["type"] = "CONTINUOUS.INTEGER"
           feature_info["feature_type_reason"] = "Integer column with many unique values."
       else:
           feature_info["type"] = "CONTINUOUS.FLOAT"
           feature_info["feature_type_reason"] = "Float column with many unique values."
   ```

5. **Categorical Analysis**
   ```python
   if feature_info["type"] == "CATEGORICAL.NOMINAL":
       if unique_ratio > unique_ratio_threshold:
           feature_info["type"] = "TO_BE_REMOVED"
           feature_info["warning"] = True
           feature_info["feature_type_reason"] = f"Too many unique values: {unique_count} of {non_missing_count} non-missing values are unique."
       elif check_ordinal(converted_series):
           feature_info["type"] = "CONTINUOUS.INTEGER" if is_integer_like(converted_series) else "CONTINUOUS.FLOAT"
           feature_info["feature_type_reason"] += " Detected as ordinal and changed to type CONTINUOUS; should be checked."
           feature_info["warning"] = True
   ```

6. **Unit Detection**
   ```python
   if unit_detection:
       unit_result = detect_units_in_series(non_missing_series)
       if unit_result["unit_detected"]:
           feature_info["unit"] = unit_result["unit"]
           feature_info["unit_detection_details"] = unit_result
           if unit_result["consistent_units"]:
               # Convert to base units if possible
               feature_info["statistics"]["converted"] = True
   ```

7. **Temporal Analysis**
   ```python
   if perform_temporal_type_analyzing:
       if pd.api.types.is_datetime64_any_dtype(non_missing_series):
           feature_info["type"] = "TEMPORAL.DATETIME"
           feature_info["feature_type_reason"] = "Detected datetime data type."
       elif is_temporal_duration(non_missing_series):
           feature_info["type"] = "TEMPORAL.DURATION"
           feature_info["feature_type_reason"] = "Detected temporal duration data."
   ```

## Column Drop Analysis

### Drop Analysis Process

The analyzer evaluates columns for potential dropping through multiple checks:

1. **Basic Checks**
   ```python
   # Check missing values
   if missing_rate > missing_threshold:
       mark_for_dropping("high_missing")

   # Check constant values
   if value_counts.iloc[0] >= constant_threshold:
       mark_for_dropping("constant_values")
   ```

2. **Duplicate Column Detection**
   ```python
   if perform_duplicate_columns:
       duplicate_cols = data.columns[data.T.duplicated()]
       mark_for_dropping("duplicate_columns")
   ```

3. **Statistical Analysis**
   ```python
   # Variance check
   if perform_zero_variance:
       selector = VarianceThreshold(threshold=0.0)
       zero_var_cols = get_zero_variance_columns()

   # VIF analysis
   if perform_high_vif and numeric_data_filled.shape[1] > 1:
       vif_data = calculate_vif(numeric_data_filled)
       high_vif_cols = vif_data[vif_data["VIF"] > vif_threshold]

   # Correlation analysis
   if perform_high_correlation:
       correlation_matrix = numeric_data_filled.corr().abs()
       high_corr_pairs = find_highly_correlated_pairs()
   ```

4. **Distribution Analysis**
   ```python
   if perform_high_skewness_kurtosis:
       skewness = col_data.skew()
       kurtosis = col_data.kurtosis()
       if abs(skewness) > skewness_threshold or abs(kurtosis) > kurtosis_threshold:
           mark_for_dropping("high_skewness_kurtosis")
   ```

## Configuration

### Main Function Parameters (`generate_metadata`)

| Parameter | Description | Default |
|-----------|-------------|---------|
| `drop_analysis_params` | Dictionary of parameters for drop analysis | `None` |
| `feature_type_params` | Dictionary of parameters for feature type analysis | `None` |
| `target_name` | Specify target column name (defaults to last column if None) | `None` |

### Feature Type Analysis Parameters

#### Control Flags
| Parameter | Description | Default |
|-----------|-------------|---------|
| `perform_temporal_type_analyzing` | Enable temporal type analysis | `True` |
| `unit_detection` | Enable unit detection and conversion | `True` |
| `unique_ratio_threshold` | Ratio threshold for unique values | 0.1 |

### Drop Analysis Parameters

#### Control Flags
| Parameter | Description | Default |
|-----------|-------------|---------|
| `skip_column_dropping` | Skip drop analysis entirely | `False` |
| `perform_high_missing` | Check high missing values | `True` |
| `perform_constant_values` | Check constant values | `True` |
| `perform_high_correlation` | Check correlations | `True` |
| `perform_high_vif` | Check VIF | `True` |
| `perform_zero_variance` | Check zero variance | `True` |
| `perform_duplicate_columns` | Check duplicates | `True` |
| `perform_high_skewness_kurtosis` | Check distributions | `True` |

#### Thresholds
| Parameter | Description | Default |
|-----------|-------------|---------|
| `correlation_threshold` | Max correlation between features | 0.95 |
| `missing_threshold` | Max missing value ratio | 0.50 |
| `constant_threshold` | Max constant value ratio | 0.95 |
| `vif_threshold` | Max Variance Inflation Factor | 5.0 |
| `skewness_threshold` | Max absolute skewness | 10.0 |
| `kurtosis_threshold` | Max absolute kurtosis | 10.0 |

### Cleaning Parameters

#### Control Flags
| Parameter | Description | Default |
|-----------|-------------|---------|
| `perform_cleaning` | Enable data cleaning steps | `False` |
| `handle_outliers` | Enable outlier handling for continuous features | `False` |

#### Options
| Parameter | Description | Default | Available Options |
|-----------|-------------|---------|-------------------|
| `handle_missing` | Method to handle missing data | `"fill"` | `"fill"`, `"drop"` |
| `imputation_option` | Method to fill missing data in continuous features | `"median"` | `"mean"`, `"median"`, `"mode"`, `"random_sample"` |

## Metadata Generation

The analyzer generates comprehensive metadata including:

1. **Feature Information**
   - Basic properties (name, type)
   - Statistical summaries
   - Unit information
   - Warning flags

2. **Type Summaries**
   - Feature counts by type
   - Feature lists by type
   - Pre/post-dropping summaries

3. **Warning Summaries**
   - Warning counts by type
   - Detailed warning information
   - Sample values for warnings

4. **Drop Analysis Results**
   - Drop recommendations
   - Detailed drop reasons
   - Drop summaries by reason

## Usage Examples

### Basic Usage
```python
analyzer = DataAnalyzer(df)
metadata = analyzer.generate_metadata()
```

### Complete Configuration
```python
metadata = analyzer.generate_metadata(
    target_name="target_column",
    feature_type_params={
        "unit_detection": True,
        "unique_ratio_threshold": 0.1,
        "perform_temporal_type_analyzing": True
    },
    drop_analysis_params={
        "skip_column_dropping": False,
        "perform_high_missing": True,
        "perform_constant_values": True,
        "perform_high_correlation": True,
        "perform_high_vif": True,
        "perform_zero_variance": True,
        "perform_duplicate_columns": True,
        "perform_high_skewness_kurtosis": True,
        "correlation_threshold": 0.95,
        "missing_threshold": 0.5,
        "constant_threshold": 0.95,
        "vif_threshold": 5.0,
        "skewness_threshold": 10.0,
        "kurtosis_threshold": 10.0
    },
    cleaning_params={
        "perform_cleaning": True,
        "handle_missing": "fill",
        "imputation_option": "median",
        "handle_outliers": False
    }
)
```

## Best Practices

1. **Pre-Analysis Setup**
   - Review data types and formats
   - Consider domain-specific requirements
   - Set appropriate thresholds

2. **Analysis Configuration**
   - Enable relevant analysis types
   - Adjust thresholds for dataset size
   - Consider computational resources

3. **Post-Analysis Review**
   - Validate drop recommendations
   - Check warning messages
   - Review type assignments

4. **Documentation**
   - Record configuration changes
   - Document drop decisions
   - Track warning resolutions 