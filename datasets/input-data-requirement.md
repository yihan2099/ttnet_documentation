# Input Data Requirements

This page provides information about the requirements for input data in TTNet.

## File Formats

TTNet supports multiple file formats for input data:

- **CSV**: Comma-separated values files
- **Parquet**: Columnar storage format
- **JSON**: JavaScript Object Notation
- **Excel**: Microsoft Excel files (.xlsx, .xls)
- **SQL**: Data from SQL databases

## Data Structure Requirements

### General Requirements

- **Headers**: All datasets must include column headers
- **Unique Identifiers**: Each dataset should have at least one column that uniquely identifies each row
- **Consistency**: Data formats should be consistent within columns
- **Completeness**: Critical fields should have minimal missing values

### Tabular Data

For tabular datasets, TTNet has the following requirements:

- **Rectangular Structure**: All rows must have the same number of columns
- **Feature Naming**: Column names should be unique and descriptive
- **Row Ordering**: No specific row ordering is required, unless specified otherwise
- **Data Types**: Each column should contain a single data type (numeric, categorical, datetime, etc.)

## Data Quality Expectations

While the system performs automatic data quality checks, your input data should ideally:

- **Have minimal missing values**: High-quality datasets have minimal NULL or empty values
- **Avoid duplicate records**: Datasets should not contain duplicate rows
- **Maintain consistent formatting**: Date formats, numerical representations, etc., should be consistent
- **Follow appropriate ranges**: Numerical values should fall within expected ranges
- **Use appropriate precision**: Numerical values should have appropriate precision

## Special Data Types

### Temporal Data

- **Date Format**: ISO format (YYYY-MM-DD) is preferred for dates
- **Timestamp Format**: ISO format with time zone information is preferred
- **Time Series**: Time series data should have consistent time intervals

### Categorical Data

- **Coding**: Categorical variables should be properly coded
- **Hierarchy**: Hierarchical categories should preserve the hierarchy information
- **Binary Data**: Binary variables should be consistently represented (e.g., 0/1, True/False)

### Numerical Data

- **Scaling**: Numerical features should be appropriately scaled for optimal model performance
- **Units**: Units of measurement should be consistent and documented
- **Precision**: Appropriate precision for the specific use case

## Dataset Size Considerations

- **Minimum Size**: Most models require a minimum of several hundred records for reliable training
- **Maximum Size**: There is no hard upper limit, but processing time may increase for very large datasets
- **Dimensionality**: Very high-dimensional data may require feature selection or dimensionality reduction

## Schema Evolution

For datasets that change over time:

- **Column Additions**: New columns can be added to existing datasets
- **Column Removals**: Existing columns can be removed, but this may require model retraining
- **Data Type Changes**: Changes to column data types should be avoided when possible

## Validation Process

TTNet validates input data against these requirements through:

1. **Automatic Validation**: Automatic checks during data import
2. **Schema Validation**: Validation against predefined schemas
3. **Quality Metrics**: Calculation of quality metrics for assessment
4. **Error Reporting**: Detailed error reports for failing validations 