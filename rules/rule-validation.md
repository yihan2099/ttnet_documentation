# Rule Validation Documentation

## Overview

The validation framework is designed to evaluate rule-based systems based on five key criteria:
1. **Structure Validation:** Ensures the ruleset is well-structured and contains the required components.
2. **Feature Definition Validation:** Verifies the correctness and consistency of feature definitions.
3. **Continuous Features Validation:** Checks that continuous features are used appropriately.
4. **Rules Validation:** Validates individual rules for syntax, logic, and consistency.
5. **Semantic Consistency Validation:** Ensures the ruleset is free of inconsistencies, unused features, and coverage gaps.

---

## Validation Criteria

### 1. Structure Validation

**Purpose:**
Ensure the rules dictionary is complete, well-formed, and adheres to the expected structure.

**Requirements:**
- The rules dictionary must contain the following keys:
  - `description`: (string) A description of the ruleset.
  - `task_type`: (string) Specifies the task type (`classification` or `regression`).
  - `input_feature_names`: (list) Names of all input features.
  - `input_feature_classes`: (dict) Mapping of categorical features to their possible values.
  - `continuous_features`: (list) A list of continuous features.
  - `feature_types`: (dict) Mapping of all features to their types (`categorical` or `continuous`).
  - `output_classes`: (list) A list of output classes for classification tasks.
  - `rules`: (list) A list of rules.
- Each key must have a valid value of the correct data type.
- `task_type` must be either `classification` or `regression`.

**Validation Process:**
1. Check for the presence of all required keys.
2. Validate that each key has the correct data type.
3. Ensure the `task_type` is valid.

---

### 2. Feature Definition Validation

**Purpose:**
Validate the definitions of features to ensure they are consistent across the ruleset.

**Requirements:**
1. **Consistency Between Features:**
   - Features in `input_feature_names` must appear in `feature_types`.
   - Features in `feature_types` must appear in `input_feature_names`.
2. **Categorical Features:**
   - Must be present in `input_feature_names`.
   - Must not appear in `continuous_features`.
   - Must be of type `categorical` in `feature_types`.
3. **Continuous Features:**
   - Must be present in `input_feature_names`.
   - Must not appear in `input_feature_classes`.
   - Must be of type `continuous` in `feature_types`.

**Validation Process:**
1. Cross-check `input_feature_names` with `feature_types`.
2. Verify that features listed in `input_feature_classes` are categorized as categorical.
3. Verify that features listed in `continuous_features` are categorized as continuous.

---

### 3. Continuous Features Validation

**Purpose:**
Ensure continuous features are correctly defined and used appropriately in rule conditions.

**Requirements:**
1. All continuous features must:
   - Appear in `input_feature_names`.
   - Not appear in `input_feature_classes`.
   - Be defined as `continuous` in `feature_types`.
2. Rule conditions involving continuous features:
   - Must use valid comparison operators (`>`, `<`).
   - Must include valid numeric thresholds (convertible to float).

**Validation Process:**
1. Validate the definition of continuous features.
2. Check rule conditions to ensure continuous features are used with comparison operators.
3. Ensure thresholds in conditions are valid numerical values.

---

### 4. Rules Validation

**Purpose:**
Validate individual rules to ensure they are syntactically and semantically valid.

**Requirements:**
1. **Structure Validation:**
   - Each rule must contain:
     - `r`: (string) The condition for the rule.
     - `w`: (dict) A dictionary mapping class names to weights.
2. **Condition Syntax Validation:**
   - Conditions support:
     - AND operations using `&`
     - OR operations using `|`
   - Operators:
     - Comparison operators: `>`, `<`, `>=`, `<=`
     - Equality operators: `=`, `==`
     - Inequality operator: `!=`
   - For categorical features:
     - Use `=`, `==`, or `!=` operators
     - Reference valid values from `input_feature_classes`
   - For continuous features:
     - Use `>`, `<`, `>=`, or `<=` operators
     - Use valid numeric thresholds
3. **Class Weight Validation:**
   - Class names in weights must:
     - Be valid class names from `output_classes`
     - Be unique within a single rule
   - Weight values must be numeric (int or float)

**Example Valid Rule:**
```json
{
  "r": "species=Zorkian & temperature>100 & gravity_force<9.8",
  "w": {
    "Peaceful": 0.8,
    "Isolationist": 0.2,
    "Enigmatic": 0.3
  }
}
```

**Example Valid Complex Rule:**
```json
{
  "r": "(temperature>=150 & gravity_force<=10.0) | (species==Blibnob & atmosphere_preference!=MethaneBased)",
  "w": {
    "Aggressive": 0.7,
    "Expansionist": 0.3,
    "Peaceful": -0.2
  }
}
```

---

### 5. Semantic Consistency Validation

**Purpose:**
Ensure the ruleset is logically sound and free from inconsistencies or gaps.

**Requirements:**
1. **Unused Features Detection:**
   - Identify features in `input_feature_names` not used in any rule conditions.
2. **Output Class Coverage:**
   - Ensure all output classes are covered by at least one rule.
3. **Conflict Detection:**
   - Identify rules with overlapping conditions that conflict in weights for the same class.

**Validation Process:**
1. Cross-check features used in rule conditions with `input_feature_names`.
2. Analyze rules to ensure all `output_classes` are covered.
3. Detect conflicting conditions or overlapping rules.

---

## Validation Workflow

1. **Load Ruleset:**
   Begin by loading the rules dictionary.

2. **Run Validations:**
   Execute validations for each of the five criteria:
   - Structure Validation
   - Feature Definition Validation
   - Continuous Features Validation
   - Rules Validation
   - Semantic Consistency Validation

3. **Generate Results:**
   Produce detailed validation results, including:
   - Errors: Critical issues that prevent the ruleset from functioning.
   - Warnings: Non-critical issues like unused features or potential conflicts.
