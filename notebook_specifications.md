
# Operational Risk Assessment Lifecycle Simulator

## Notebook Overview

### Learning Goals
This notebook provides an interactive simulation designed to enhance understanding of the Operational Risk Assessment Lifecycle, a foundational framework for managing operational risk within an organization [2, Chapter 4 – Risk Assessment]. By engaging with the simulation, users will:
- Understand the key phases and iterative nature of the operational risk assessment lifecycle, including risk identification, control assessment, and management validation [2, page 20-21].
- Differentiate between inherent risk and residual risk and their relationship [2, page 37-38].
- Evaluate the impact of control effectiveness on mitigating operational risks [2, page 31-32].
- Learn how to identify and prioritize issues and design action plans within a risk management framework [2, page 36].

### Expected Outcomes
Upon completing this notebook, users will be able to:
- Interpret the interplay between inherent risk, control effectiveness, and residual risk within a simulated environment.
- Analyze the immediate impact of adjusting control effectiveness on the calculated residual risk.
- Visualize trends and relationships in operational risk data.
- Gain a practical, interactive understanding of risk mitigation strategies, reinforcing concepts from the 'Two Sample Approaches for Residual Risk Rating' [2, page 38].

## Mathematical and Theoretical Foundations

### The Operational Risk Assessment Lifecycle
Operational risk management is an iterative process that involves identifying, assessing, mitigating, and monitoring risks to an organization's operations. A critical part of this cycle is understanding the interplay between inherent risk, control effectiveness, and the resulting residual risk.

- **Inherent Risk**: This is the level of risk existing in an activity or process before any controls or mitigation measures are applied. It represents the "raw" exposure to a potential loss event.
- **Control Effectiveness**: This refers to the efficacy of the controls implemented to prevent or detect operational risk events. Controls can be classified as 'Preventative' (designed to stop an event from occurring) or 'Detective' (designed to identify an event after it occurs). For assessment purposes, control effectiveness is typically rated qualitatively (e.g., 'Effective', 'Partially Effective', 'Ineffective').
- **Residual Risk**: This is the level of risk that remains after all controls and mitigation actions have been implemented and are operating. It represents the actual risk exposure an organization faces. The relationship between inherent risk, control effectiveness, and residual risk is complex; ineffective controls can, in some scenarios, lead to a residual risk level that is higher than the inherent risk, particularly when legal or regulatory violations are involved [2, page 38].

### Residual Risk Score Calculation
For the purpose of this simulation, the `ResidualRiskScore` is derived through a lookup logic based on `InherentRiskRating` and `ControlEffectivenessRating`, mirroring the qualitative tables presented in the 'Residual Risk' section of the handbook [2, page 38]. This approach captures the additive nature described for combining these factors.

First, the qualitative ratings are assigned numerical scores:
- **Inherent Risk Score ($S_{IR}$)**:
    - 'Low' = 1
    - 'Medium' = 2
    - 'High' = 3

- **Control Effectiveness Score ($S_{CE}$)**:
    - 'Ineffective' = 1
    - 'Partially Effective' = 2
    - 'Effective' = 3

The `RawCombinedScore` is then calculated using the following formula, which translates the inverse relationship of `ControlEffectivenessRating` into an additive component:

$$ \text{RawCombinedScore} = S_{IR} + (4 - S_{CE}) $$

In this formula, a higher `RawCombinedScore` indicates a higher level of residual risk. The term $(4 - S_{CE})$ ensures that:
- For an 'Effective' control ($S_{CE} = 3$), the contribution to the `RawCombinedScore` is $(4 - 3) = 1$.
- For a 'Partially Effective' control ($S_{CE} = 2$), the contribution is $(4 - 2) = 2$.
- For an 'Ineffective' control ($S_{CE} = 1$), the contribution is $(4 - 1) = 3$.
This effectively means that lower control effectiveness (lower $S_{CE}$) results in a higher additive component, thus increasing the `RawCombinedScore`.

Finally, the `RawCombinedScore` is mapped to a qualitative `ResidualRiskRating` ('Low', 'Medium', 'High') based on predefined thresholds. These thresholds are calibrated to align with the qualitative residual risk matrices presented in the PRMIA handbook, ensuring the simulation reflects the conceptual model. For example:
- If `RawCombinedScore` $\le \text{Threshold}_1$, then `ResidualRiskRating` = 'Low'.
- If $\text{Threshold}_1 < \text{RawCombinedScore} \le \text{Threshold}_2$, then `ResidualRiskRating` = 'Medium'.
- If `RawCombinedScore` $> \text{Threshold}_2$, then `ResidualRiskRating` = 'High'.

## Code Requirements

### 1. Initial Setup and Data Handling

#### Expected Libraries
-   **Data Generation**: Libraries such as `faker` (for realistic categorical names), `numpy.random` (for numeric and time-series data), and `pandas` (for DataFrame construction) will be used to create the synthetic dataset.
-   **Data Manipulation and Analysis**: `pandas` will be essential for data loading, validation, cleaning, transformation, and calculations.
-   **Visualization**: `matplotlib.pyplot` and `seaborn` will be utilized for generating static plots. For interactivity, `plotly.express` or `altair` will be preferred.
-   **Interactive Widgets**: `ipywidgets` will be used to create user-adjustable sliders and dropdowns.

#### Input/Output Expectations
-   **Input**:
    *   **Data File**: The notebook will first check for a `risk_assessment_data.csv` file. If not found, a synthetic dataset will be generated programmatically with realistic numeric, categorical, and time-series fields (`BusinessUnit`, `RiskName`, `InherentRiskRating`, `ControlType`, `ControlEffectivenessRating`, `SimulatedLossFrequency`, `AssessmentDate`). This generation ensures the notebook can run even without an explicit user data upload (optional lightweight sample $\le 5$ MB).
    *   **User Interactions**: Users will provide input via interactive `ipywidgets` (sliders to adjust `ControlEffectivenessRating` for specific risk contexts, and dropdowns to filter by `BusinessUnit` or `RiskName`).
-   **Output**:
    *   **Console Logs**: Detailed logs will be printed for data validation (confirming expected column names, data types, primary-key uniqueness for `RiskID`, and reporting any missing values in critical fields). Summary statistics for numeric columns will also be logged.
    *   **DataFrame Output**: The original DataFrame will be enriched with calculated columns: `InherentRiskScore`, `ControlEffectivenessScore`, `RawCombinedScore`, and `ResidualRiskRating`.
    *   **Visualizations**: Three core visualizations (trend plot, relationship plot, and aggregated comparison plots) will be generated and displayed.

#### Logical Flow for Data Handling
1.  **Markdown Cell**: Introduce the data loading/generation step, explaining the nature of the synthetic dataset.
2.  **Code Cell (Data Generation/Loading)**:
    *   Define a function, e.g., `generate_synthetic_data()`, that creates a pandas DataFrame mimicking `risk_assessment_data.csv` with the specified columns and realistic values.
    *   Implement logic to check if `risk_assessment_data.csv` exists. If it does, load it. Otherwise, call `generate_synthetic_data()` and save the output to `risk_assessment_data.csv`.
3.  **Markdown Cell**: Explain the importance of data validation.
4.  **Code Cell (Data Validation)**:
    *   Implement checks for column names and data types.
    *   Assert `RiskID` (if generated) column for uniqueness.
    *   Verify and report on missing values in key columns.
    *   Generate and display summary statistics (`.describe()`) for numeric columns.

### 2. Operational Risk Calculation and Modeling

#### Algorithms/Functions to be Implemented
1.  **`map_categorical_to_numerical(df)`**:
    *   **Purpose**: Convert categorical risk ratings to their defined numerical scores.
    *   **Input**: The DataFrame containing `InherentRiskRating` and `ControlEffectivenessRating` columns.
    *   **Logic**:
        *   Create a mapping dictionary for `InherentRiskRating` ('Low': 1, 'Medium': 2, 'High': 3).
        *   Create a mapping dictionary for `ControlEffectivenessRating` ('Ineffective': 1, 'Partially Effective': 2, 'Effective': 3).
        *   Apply these mappings to create new columns `InherentRiskScore` and `ControlEffectivenessScore`.
    *   **Output**: DataFrame with added numerical score columns.
2.  **`calculate_residual_scores(df)`**:
    *   **Purpose**: Compute the `RawCombinedScore` and `ResidualRiskRating` based on the mapped numerical scores.
    *   **Input**: DataFrame containing `InherentRiskScore` and `ControlEffectivenessScore`.
    *   **Logic**:
        *   Apply the formula `RawCombinedScore = S_{IR} + (4 - S_{CE})` to each row to compute `RawCombinedScore`.
        *   Define thresholds for mapping `RawCombinedScore` to 'Low', 'Medium', 'High' `ResidualRiskRating` categories. These thresholds will be carefully chosen to emulate the qualitative matrices in [2, page 38].
        *   Apply the mapping logic to derive the `ResidualRiskRating` for each row.
    *   **Output**: DataFrame with added `RawCombinedScore` and `ResidualRiskRating` columns.
3.  **`apply_user_adjustments(df, selected_business_unit=None, selected_risk_name=None, adjusted_control_effectiveness=None)`**:
    *   **Purpose**: Simulate the impact of user-adjusted control effectiveness on residual risk.
    *   **Input**: The base DataFrame, and optional parameters from user interaction: `selected_business_unit` (string), `selected_risk_name` (string), and `adjusted_control_effectiveness` (string: 'Effective', 'Partially Effective', 'Ineffective').
    *   **Logic**:
        *   Create a copy of the DataFrame to avoid modifying the original data for interactive changes.
        *   If `adjusted_control_effectiveness` is provided, identify the rows corresponding to `selected_business_unit` AND/OR `selected_risk_name`.
        *   For these filtered rows, temporarily override their `ControlEffectivenessRating` to the `adjusted_control_effectiveness` value.
        *   Re-run the `map_categorical_to_numerical` and `calculate_residual_scores` functions on this modified DataFrame slice.
    *   **Output**: An updated DataFrame reflecting the temporary adjustments and their impact on residual risk.

### 3. User Interaction

#### Interactive Widgets
-   **Dropdowns**:
    *   A dropdown widget to filter the dataset by `BusinessUnit`.
    *   A dropdown widget to filter the dataset by `RiskName`.
-   **Sliders**:
    *   A slider widget allowing users to adjust the `ControlEffectivenessRating` for risks belonging to the selected `BusinessUnit` and/or `RiskName`. This slider will represent the three levels ('Ineffective', 'Partially Effective', 'Effective') numerically (1 to 3).
-   **Inline Help**: Tooltips will be provided for each interactive control. For instance, hovering over the control effectiveness slider will display: "Adjusting control effectiveness directly impacts the calculated residual risk, as discussed in the 'Residual Risk' section [2, page 38]."

#### Logic for User Interaction
-   The interactive widgets will be linked to a display function that dynamically updates the visualizations.
-   Changes in dropdowns will filter the underlying data for all subsequent analyses and plots.
-   Changes in the control effectiveness slider will trigger `apply_user_adjustments` to recalculate residual risk for the affected data subset, and then update the relevant plots (especially the Relationship and Aggregated Comparison plots) to show the immediate impact.

### 4. Core Visualizations

All visualizations will adhere to style guidelines: adopt a color-blind-friendly palette and ensure font size $\ge 12$ pt. Clear titles, labeled axes, and legends will be supplied. Interactivity (e.g., hovering for details, zooming/panning) will be enabled where supported by the chosen visualization library. A static fallback (saved PNG) will be generated and offered if interactive libraries are unavailable or the environment does not support them.

1.  **Trend Plot (Line Plot)**
    *   **Purpose**: Illustrate the simulated `ResidualRiskScore` over `AssessmentDate` for selected business units, showing iterative improvement or deterioration of risk posture.
    *   **X-axis**: `AssessmentDate` (time-series).
    *   **Y-axis**: `ResidualRiskScore` (numeric).
    *   **Lines/Color**: Separate lines for each `BusinessUnit` (filtered by user dropdown).
    *   **Interactivity**: Enable hovering over points to display exact `AssessmentDate` and `ResidualRiskScore`. Support zoom and pan.
    *   **Fallback**: Save plot as `trend_plot.png`.

2.  **Relationship Plot (Scatter Plot)**
    *   **Purpose**: Examine the correlation between `InherentRiskRating` and `ControlEffectivenessRating`, with the resulting `ResidualRiskRating` indicated by color. This directly visualizes the 'Two Sample Approaches for Residual Risk Rating' [2, page 38].
    *   **X-axis**: `InherentRiskScore` (1, 2, 3 representing 'Low', 'Medium', 'High').
    *   **Y-axis**: `ControlEffectivenessScore` (1, 2, 3 representing 'Ineffective', 'Partially Effective', 'Effective').
    *   **Color**: `ResidualRiskRating` ('Low', 'Medium', 'High').
    *   **Annotation**: Add labels or a grid to clearly show how combinations of inherent risk and control effectiveness map to residual risk categories.
    *   **Interactivity**: Enable hovering over points to show `BusinessUnit`, `RiskName`, `InherentRiskRating`, `ControlEffectivenessRating`, and `ResidualRiskRating`.
    *   **Fallback**: Save plot as `relationship_plot.png`.

3.  **Aggregated Comparison Plots**
    *   **Heatmap**
        *   **Purpose**: Provide a quick overview of high-risk areas by displaying the aggregated `ResidualRiskRating` across `BusinessUnit` and `RiskName` categories.
        *   **X-axis**: `BusinessUnit`.
        *   **Y-axis**: `RiskName`.
        *   **Color Intensity**: Represents the aggregated `ResidualRiskScore` (e.g., mean or mode of the numerical score) for each combination.
        *   **Interactivity**: Enable hovering to show exact aggregated `ResidualRiskScore` for each cell.
        *   **Fallback**: Save plot as `heatmap_plot.png`.
    *   **Bar Chart**
        *   **Purpose**: Compare the distribution of `ControlType` (preventative vs. detective) across the organization or selected subsets.
        *   **X-axis**: `ControlType`.
        *   **Y-axis**: Count or percentage of risks.
        *   **Interactivity**: Enable hovering to show exact counts/percentages.
        *   **Fallback**: Save plot as `barchart_plot.png`.

## Additional Notes or Instructions

### Assumptions and Constraints
-   The lab is designed to execute end-to-end on a mid-spec laptop (8 GB RAM) in fewer than 5 minutes. Performance will be prioritized during implementation.
-   Only open-source Python libraries available on PyPI will be used, ensuring broad accessibility.
-   All major computational and analytical steps will be accompanied by clear Markdown narrative cells explaining "what" is happening and "why", along with detailed inline code comments.
-   The synthetic dataset generation will include a `RiskID` field to support primary-key uniqueness validation.

### Customization Instructions
-   Users are encouraged to modify the parameters within the synthetic data generation function to explore different organizational structures, risk profiles, and control environments.
-   The thresholds used to map `RawCombinedScore` to `ResidualRiskRating` can be adjusted by users to observe how changes in risk appetite affect categorization.
-   Interactive dropdowns and sliders allow dynamic filtering and adjustment of parameters, enabling learners to conduct "what-if" analyses.

### Inline Help
-   Every user interaction control (sliders, dropdowns) will feature inline help text or tooltips. These will briefly explain the control's function and provide relevant context, often citing specific sections of the PRMIA Operational Risk Manager Handbook [2].

### References
[1] Upper Extremity Active Range of Motion - Sitting, Aurora Health Care, https://ahc.aurorahealthcare.org/fywb/x06281.pdf
[2] Operational Risk Manager Handbook, PRMIA Publications, Updated November, 2015.
[3] Upper Body: Range of Motion Exercises - Self-Directed for the Neck and Arms, Sarah Housman, MS, OTR/L; Mary Pearson OTR/L, SRALAB, https://www.sralab.org/sites/default/files/2017-05/Upper+Body+ROM+Self+Directed+for+the+Neck+and+Arms.pdf
[4] Object-Role Modeling Fundamentals, orm.net, https://orm.net/resources
