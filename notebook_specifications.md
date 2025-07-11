
# Technical Specification for Jupyter Notebook: Operational Risk Assessment Lifecycle Simulator

This document details the technical specifications for a Jupyter Notebook designed to simulate the Operational Risk Assessment Lifecycle. The notebook will guide users through the concepts of inherent risk, control effectiveness, and residual risk using interactive elements and clear visualizations.

## 1. Notebook Overview

### Learning Goals

Upon completion of this notebook, users will be able to:
- Understand the key phases and iterative nature of the operational risk assessment lifecycle, including risk identification, control assessment, and management validation, as outlined in [2, page 20-21].
- Differentiate between inherent risk and residual risk, and comprehend their fundamental relationship [2, page 37-38].
- Evaluate the impact of control effectiveness on mitigating operational risks, as discussed in [2, page 31-32].
- Learn how to identify and prioritize issues and design effective action plans within a risk management framework [2, page 36].
- Gain key insights into the concepts and dynamics presented in the "Operational Risk Manager Handbook" [2] and the generated synthetic data.

### Expected Outcomes

Users will interact with a simulated environment to:
- Explore the operational risk assessment lifecycle through a step-by-step logical flow.
- Observe how changes in `ControlEffectivenessRating` dynamically impact `ResidualRiskScore` and `ResidualRiskRating`.
- Analyze risk trends, relationships, and aggregated views through interactive and informative visualizations.
- Develop a practical understanding of risk mitigation strategies by manipulating simulation parameters.

## 2. Mathematical and Theoretical Foundations

This section defines the core concepts and the mathematical logic underpinning the simulation.

### Operational Risk Assessment Lifecycle

The notebook will introduce the iterative nature of the operational risk assessment lifecycle, which is a foundational framework for managing operational risk. It typically involves stages such as risk identification, control assessment, and management validation. This lifecycle is depicted and explained on pages 20-21 of the "Operational Risk Manager Handbook" [2].

### Inherent Risk vs. Residual Risk

- **Inherent Risk**: This is the level of risk before any controls or mitigation strategies are applied. It represents the raw risk exposure.
- **Residual Risk**: This is the level of risk remaining after considering the effectiveness of controls and mitigation activities. It is the risk that an organization is ultimately exposed to.
The handbook clarifies that while conceptually controls reduce inherent risk to arrive at residual risk, it is not a simple subtraction but rather a complex interplay [2, page 37-38].

### Control Effectiveness

Controls are mechanisms put in place to mitigate risks. Their effectiveness determines how much they reduce inherent risk. Controls can be `Preventative` (designed to prevent a risk event from occurring) or `Detective` (designed to identify a risk event either concurrently with its execution or after its occurrence). The assessment of control effectiveness is detailed on pages 31-32 of the handbook [2].

### Mathematical Model for Residual Risk

For the purpose of this simulation, the `ResidualRiskScore` is determined by a simplified numerical mapping of `InherentRiskRating` ($S_{IR}$) and `ControlEffectivenessRating` ($S_{CE}$), aiming to mirror the qualitative tables presented in the "Residual Risk" section of the handbook [2, page 38].

**Numerical Mapping of Categorical Ratings:**
- **Inherent Risk Rating ($S_{IR}$)**:
    - 'Low' = 1
    - 'Medium' = 2
    - 'High' = 3
- **Control Effectiveness Rating ($S_{CE}$)**:
    - 'Ineffective' = 1
    - 'Partially Effective' = 2
    - 'Effective' = 3

**Raw Combined Score Calculation (Illustrative):**
A raw combined score, $\text{RawCombinedScore}$, is calculated using the following formula to conceptually demonstrate the interplay where decreasing control effectiveness ($S_{CE}$) leads to an increased combined score:
$$ \text{RawCombinedScore} = S_{IR} + (4 - S_{CE}) $$
Where a higher $\text{RawCombinedScore}$ indicates higher residual risk. This formula provides a numerical basis for evaluating the combined impact of inherent risk and control effectiveness.

**Mapping RawCombinedScore to Residual Risk Categories:**
The $\text{RawCombinedScore}$ will then be mapped to the qualitative `ResidualRiskRating` categories ('Low', 'Medium', 'High'). This categorical mapping will directly reflect the lookup logic of the primary `Residual risk chart` (Table 1) in [2, page 38], ensuring consistency with the handbook's qualitative outcomes.

The specific mapping logic from $\text{RawCombinedScore}$ to `ResidualRiskRating` will be:
- If $2 \le \text{RawCombinedScore} \le 3$: 'Low'
- If $4 \le \text{RawCombinedScore} \le 5$: 'Medium'
- If $\text{RawCombinedScore} = 6$: 'High'

For example, an item with 'High' `InherentRiskRating` ($S_{IR}=3$) and 'Effective' `ControlEffectivenessRating` ($S_{CE}=3$) would yield a $\text{RawCombinedScore}$ of $3 + (4-3) = 4$. Based on the mapping logic, this would result in a 'Medium' `ResidualRiskRating`, which aligns with the corresponding entry in Table 1 on page 38 of the handbook [2].

## 3. Code Requirements

The notebook will be structured with distinct code and markdown cells to facilitate understanding. Each major step will include both code comments and brief narrative cells describing "what" is happening and "why".

### Expected Libraries

Only open-source Python libraries available on PyPI will be used. Core libraries will include:
- `pandas` for efficient data manipulation and analysis.
- `numpy` for core numerical operations.
- `matplotlib.pyplot` for creating static plots.
- `seaborn` for generating aesthetically pleasing statistical data visualizations.
- `ipywidgets` for implementing interactive controls such as sliders and dropdowns.
- `datetime` (or equivalent like `arrow`) for robust handling of time-series data.

### Input/Output Expectations

- **Input**:
    - A synthetic CSV file named `risk_assessment_data.csv` will serve as the primary input. This file is expected to contain realistic numeric, categorical, and time-series fields including `BusinessUnit` (categorical), `RiskName` (categorical), `InherentRiskRating` (categorical: 'Low', 'Medium', 'High'), `ControlType` (categorical: 'Preventative', 'Detective'), `ControlEffectivenessRating` (categorical: 'Effective', 'Partially Effective', 'Ineffective'), `SimulatedLossFrequency` (numeric), and `AssessmentDate` (time-series).
    - An optional lightweight sample (≤ 5 MB) of this dataset will be provided, enabling the notebook to run without user-supplied data.
- **Output**:
    - Console output logging data validation checks, summary statistics for numeric columns, and key calculation steps.
    - An augmented pandas DataFrame that includes newly calculated columns such as `S_IR`, `S_CE`, `RawCombinedScore`, and `ResidualRiskRating`.
    - A series of informative visualizations (line plots, scatter plots, heatmaps, bar charts) displaying the simulation results and insights.
    - Static PNG fallback images will be generated for interactive plots, ensuring functionality in environments where interactive libraries are not fully supported.

### Algorithms or Functions to be Implemented

1.  **Synthetic Data Generation Function (`generate_synthetic_data`)**:
    - **Purpose**: To create a synthetic `risk_assessment_data.csv` if an input file is not provided by the user.
    - **Parameters**: `num_records` (integer for the number of rows), `start_date` (datetime object for the beginning of the time series), `end_date` (datetime object for the end of the time series), and lists of possible values for categorical fields.
    - **Logic**: Generates data for `BusinessUnit`, `RiskName`, `InherentRiskRating`, `ControlType`, `ControlEffectivenessRating`, `SimulatedLossFrequency` (e.g., random integers or floats within a range), and `AssessmentDate` (random dates within the specified range). Ensures realistic distributions and relationships where appropriate.
    - **Output**: Saves the generated data to `risk_assessment_data.csv` and returns the data as a pandas DataFrame.

2.  **Data Loading and Validation Function (`load_and_validate_data`)**:
    - **Purpose**: Loads the dataset from the specified path and performs essential data quality and integrity checks.
    - **Parameters**: `file_path` (string, path to `risk_assessment_data.csv`).
    - **Logic**:
        - Loads the CSV file into a pandas DataFrame.
        - Confirms the presence of expected column names and their adherence to predefined data types (e.g., `AssessmentDate` converted to datetime, `InherentRiskRating` and `ControlEffectivenessRating` handled as categorical strings).
        - Asserts primary-key uniqueness for a `RiskID` column (if present) or a combination of columns that uniquely identifies a risk assessment entry.
        - Verifies that no missing values are present in critical fields (`InherentRiskRating`, `ControlEffectivenessRating`, `AssessmentDate`).
        - Logs summary statistics (mean, median, standard deviation, min, max) for numeric columns (`SimulatedLossFrequency`).
    - **Output**: A validated pandas DataFrame suitable for analysis. Raises an error or warning if validation fails.

3.  **Risk Scoring Logic Functions**:
    - **`map_inherent_risk_to_score(rating)`**:
        - **Purpose**: Converts a categorical `InherentRiskRating` ('Low', 'Medium', 'High') to its corresponding numerical score ($S_{IR}$).
        - **Input**: A string representing the inherent risk rating.
        - **Output**: An integer (1, 2, or 3).
    - **`map_control_effectiveness_to_score(rating)`**:
        - **Purpose**: Converts a categorical `ControlEffectivenessRating` ('Ineffective', 'Partially Effective', 'Effective') to its corresponding numerical score ($S_{CE}$).
        - **Input**: A string representing the control effectiveness rating.
        - **Output**: An integer (1, 2, or 3).
    - **`calculate_raw_combined_score(s_ir, s_ce)`**:
        - **Purpose**: Computes the `RawCombinedScore` based on the numerical inherent risk and control effectiveness scores.
        - **Inputs**: $S_{IR}$ (integer), $S_{CE}$ (integer).
        - **Output**: An integer representing the raw combined score.
    - **`determine_residual_risk_rating(inherent_rating, control_effectiveness_rating)`**:
        - **Purpose**: Determines the final qualitative `ResidualRiskRating` ('Low', 'Medium', 'High') by directly implementing the lookup logic from Table 1 in [2, page 38]. This function ensures the categorical output strictly adheres to the handbook's qualitative mapping.
        - **Inputs**: Categorical `inherent_rating` (string), categorical `control_effectiveness_rating` (string).
        - **Output**: A string ('Low', 'Medium', or 'High') representing the residual risk rating.
    - **`apply_risk_scoring(dataframe)`**:
        - **Purpose**: Orchestrates the application of all risk mapping and calculation functions to the input DataFrame, adding the derived score and rating columns.
        - **Input**: A pandas DataFrame.
        - **Output**: The DataFrame with new `S_IR`, `S_CE`, `RawCombinedScore`, and `ResidualRiskRating` columns.

4.  **Interactive Simulation Update Function (`update_simulation`)**:
    - **Purpose**: Recalculates and updates the `ResidualRiskScore` and `ResidualRiskRating` dynamically based on user interactions with sliders and dropdowns.
    - **Parameters**: The original DataFrame, user-selected `business_unit_filter` (string or list of strings), `risk_name_filter` (string or list of strings), and `control_effectiveness_adjustment` (numeric value from slider, e.g., representing a shift in control effectiveness).
    - **Logic**:
        - Filters the DataFrame based on `business_unit_filter` and `risk_name_filter`.
        - Applies the `control_effectiveness_adjustment` to the `ControlEffectivenessRating` within the filtered subset. This adjustment simulates an improvement or deterioration in controls (e.g., mapping 'Partially Effective' to 'Effective' for the selected subset based on adjustment).
        - Re-applies the `calculate_raw_combined_score` and `determine_residual_risk_rating` functions to the adjusted data.
        - Triggers the re-generation of all relevant visualizations to reflect the new residual risk levels.
    - **Output**: Updates the displayed visualizations.

### Visualization Requirements

All visuals will adopt a color-blind-friendly palette, ensure font size $\ge 12$ pt, and include clear titles, labeled axes, and legends. Interactivity (hovering for details, zooming) will be enabled where the environment supports it (e.g., using libraries that enable this by default, like Plotly, though the specification must not mention specific libraries that aren't core to the general Python ecosystem). A static fallback (saved PNG) will be offered if interactive libraries are unavailable.

1.  **Trend Plot**:
    - **Type**: Line plot.
    - **Content**: Displays the simulated `ResidualRiskScore` over `AssessmentDate`.
    - **Segmentation**: Includes a dropdown widget to allow users to select and filter the plot by one or more `BusinessUnit`s, illustrating the iterative improvement or deterioration of risk posture over time for specific organizational units.

2.  **Relationship Plot**:
    - **Type**: Scatter plot.
    - **Content**: Examines the correlation between `InherentRiskRating` (mapped to numerical scores, $S_{IR}$) on one axis and `ControlEffectivenessRating` (mapped to numerical scores, $S_{CE}$) on the other.
    - **Highlighting**: The resulting `ResidualRiskRating` (categorical: 'Low', 'Medium', 'High') will be represented by different colors for data points, visually demonstrating their complex interplay as conceptualized in [2, page 38].

3.  **Aggregated Comparison Visualizations**:
    - **Heatmap**:
        - **Content**: A heatmap displaying the aggregated `ResidualRiskRating` (e.g., average numerical `RawCombinedScore` or a count/percentage of 'High' risks) across `BusinessUnit` categories on one axis and `RiskName` categories on the other.
        - **Purpose**: Provides a quick, intuitive overview of high-risk areas and concentrations across the organization.
    - **Bar Chart**:
        - **Content**: A bar chart comparing the distribution of `ControlType` ('Preventative' vs. 'Detective') across the entire organization or for selected filtered subsets.
        - **Purpose**: Offers insights into the organization's reliance on different types of controls for risk mitigation.

## 4. Additional Notes or Instructions

### Assumptions & Constraints

- The notebook is designed to execute end-to-end on a mid-spec laptop (8 GB RAM) in fewer than 5 minutes. This implies efficient data handling and computational logic.
- All code will utilize only open-source Python libraries, ensuring accessibility and ease of setup.
- All major steps within the notebook will be clearly explained through descriptive markdown cells (narratives) outlining "what" is being done and "why," complemented by in-code comments for detailed logic.
- An optional lightweight sample dataset will be bundled, allowing the notebook to run out-of-the-box even if a user does not provide their own `risk_assessment_data.csv`.
- The simulation's primary goal is conceptual understanding and demonstration of risk lifecycle components, not precise quantitative risk modeling for regulatory compliance.

### User Interaction

- **Sliders**: A dedicated slider will allow users to adjust an "overall `ControlEffectivenessRating`" for a selected risk type or business unit. This adjustment will instantly trigger recalculations and visualize the resultant changes in `ResidualRiskScore` and `ResidualRiskRating`.
    - **Inline Help**: A tooltip or brief markdown text will accompany the slider, providing context such as: "Adjusting control effectiveness directly impacts the calculated residual risk, as discussed in the 'Residual Risk' section [2, page 38]."
- **Dropdowns**: Interactive dropdown menus will enable users to filter the analysis and visualizations by `BusinessUnit` or `RiskName` to focus on specific areas of interest within the simulated data.
    - **Inline Help**: Tooltips or explanatory text will clarify the purpose of each dropdown (e.g., "Select a business unit to refine the risk analysis to a specific organizational area.").

### References

The notebook will conclude with a "References" section, providing proper attribution:
- [2] Operational Risk Manager Handbook, PRMIA Publications, Updated November, 2015.
- Any other external datasets, theoretical frameworks, or significant Python libraries (beyond those commonly known, like pandas or numpy) utilized that require specific citation.
