# Do School Size and Staffing Improve Predictions of School ACT Scores?

## Project Overview

This project investigates whether school-level characteristics—enrollment and students per teacher—help explain differences in average school ACT scores beyond socioeconomic characteristics of the communities around schools.

The analysis combines EdGap school ACT and socioeconomic data with school directory, student membership, and staff data from the National Center for Education Statistics (NCES). It focuses on U.S. high schools and compares a baseline statistical model using socioeconomic predictors with a fuller model that also includes school size and staffing.

**Research question:** Is average school ACT score predicted by socioeconomic factors, and do school size (enrollment) and staffing (students per teacher) add predictive power beyond those factors?

## Repository Contents

```text
education/
├── data/
│   ├── EdGap_data.xlsx
│   ├── ccd_sch_029_1617_w_1a_11212017.csv
│   ├── ccd_sch_052_1617_l_2a_11212017.csv
│   └── ccd_sch_059_1617_l_2a_11212017.csv
├── code/
│   └── education.ipynb
├── education_clean.csv
├── reports/
│   └── [communication document]
├── requirements.txt
└── README.md
```

## Data Sources and Description

This project uses four datasets.

### 1. EdGap school ACT and socioeconomic data

- **File:** `EdGap_data.xlsx`
- **Source:** [EdGap DATA 5100 repository](https://github.com/brian-fischer/DATA-5100)
- **EdGap site:** [EdGap.org](https://www.edgap.org/#5/37.875/-96.987)

This dataset contains each school's average ACT score (or an SAT score converted to an ACT-equivalent score) and socioeconomic measures associated with the school's census tract/community. The socioeconomic data are from the U.S. Census Bureau's American Community Survey. EdGap reports that ACT/SAT data were obtained from state departments of education or other public data releases.

Variables used in this project include:

- Average school ACT score (outcome)
- Census-tract unemployment rate
- Percentage of adults with a college degree
- Percentage of children in married-couple families
- Median household income
- Percentage of students eligible for free or reduced-price lunch

The school attendance area may include multiple census tracts, so these contextual measures should not be interpreted as exact descriptions of every student's household.

### 2. NCES school directory

- **File:** `ccd_sch_029_1617_w_1a_11212017.csv`
- **Source:** [National Center for Education Statistics — Common Core of Data, Public School Universe](https://nces.ed.gov/ccd/pubschuniv.asp)

This dataset provides identifying and descriptive information about schools. The notebook uses the school identifier (`NCESSCH`) to connect records and uses fields for school year, state, ZIP code, school type, school level, and charter status.

### 3. NCES school membership / enrollment

- **File used in the notebook:** `ccd_sch_052_1617_l_2a_11212017.csv`
- **Source:** [NCES — Common Core of Data, Public School Universe](https://nces.ed.gov/ccd/pubschuniv.asp)
- **Dataset** As dataset was too big to uplad in github the link to access it : (https://huggingface.co/datasets/anush9182/management/tree/main)

  
This dataset contains school membership counts. The notebook filters the membership indicator to select the total student count (excluding adult education) and uses `STUDENT_COUNT` as school enrollment.

### 4. NCES school staff

- **File used in the notebook:** `ccd_sch_059_1617_l_2a_11212017.csv`
- **Source:** [NCES — Common Core of Data, Public School Universe](https://nces.ed.gov/ccd/pubschuniv.asp)

This dataset provides school staffing information. The notebook uses the `TEACHERS` field as teacher full-time-equivalent count.

> **Data access note:** The NCES source page provides information and access to the public school universe files. If a source file is too large to store directly in GitHub, include a stable download link in the repository documentation and follow the course's instructions for sharing large files. Do not commit files that exceed GitHub's file-size limits.

## Data Preparation and Analysis

The analysis is implemented in [`education.ipynb`](code/education.ipynb) (adjust the link if the notebook is stored at the repository root).

1. **Load and inspect data.** Read the EdGap Excel file and NCES CSV files with pandas; inspect column names, data types, row counts, and missing values.
2. **Select and standardize fields.** Keep the school directory fields needed for analysis, select the enrollment total from the membership file, and select teacher counts from the staff file. Rename columns to consistent names.
3. **Match school records.** Standardize school IDs and merge the EdGap, directory, enrollment, and staffing data using the NCES school identifier.
4. **Clean invalid values.** Treat ACT scores outside the 1–36 range, free/reduced-lunch proportions outside 0–1, nonpositive enrollment or teacher counts, and implausible student-teacher ratios (below 3 or above 60) as missing. Retain high schools and remove duplicate school IDs.
5. **Create school-level predictors.** Calculate students per teacher as enrollment divided by teacher FTE, log-transform enrollment to reduce right skew, and create a large/small school grouping for exploratory plots.
6. **Handle missing data.** Remove records without an observed average ACT score. Use scikit-learn's `IterativeImputer` to impute missing numeric predictor values.
7. **Explore the data.** Produce distributions, correlation plots, comparisons by school size, and plots relating enrollment and student-teacher ratio to average ACT score.
8. **Fit and compare regression models.**
   - **Baseline model:** socioeconomic predictors only.
   - **Full model:** socioeconomic predictors plus log enrollment and student-teacher ratio.
   - Compare model fit with R-squared, adjusted R-squared, AIC, MAE, RMSE, and an ANOVA comparison of nested models.
9. **Assess predictor importance and robustness.** Fit a standardized-predictor model, inspect residuals, and fit an additional model controlling for state and charter status.

The notebook uses observational data, so associations should not be interpreted as proof that a factor causes higher or lower ACT scores. ACT reporting practices can also differ by state.

## Main Research Variables

| Role | Variable | Description |
|---|---|---|
| Outcome | `average_act` | School average ACT score, or ACT-equivalent score |
| Socioeconomic predictor | `rate_unemployment` | Census-tract unemployment rate |
| Socioeconomic predictor | `percent_college` | Percentage of adults with a college degree |
| Socioeconomic predictor | `percent_married` | Percentage of children in married-couple families |
| Socioeconomic predictor | `median_income` | Census-tract median household income |
| Socioeconomic predictor | `percent_lunch` | Percentage of students eligible for free/reduced-price lunch |
| School-level predictor | `log_enrollment` | Natural log of school enrollment |
| School-level predictor | `student_teacher_ratio` | Enrollment divided by teacher FTE |

## Requirements

- Python 3.9 or later
- Jupyter Notebook or JupyterLab
- pandas
- NumPy
- Matplotlib
- seaborn
- scikit-learn
- statsmodels
- openpyxl (for reading `.xlsx` files)

Install the dependencies from the repository's `requirements.txt` file:

```bash
python -m pip install -r requirements.txt
```

If a requirements file has not yet been created, these packages can be installed with:

```bash
python -m pip install pandas numpy matplotlib seaborn scikit-learn statsmodels openpyxl jupyter
```

## How to Run

1. Clone or download this repository.
2. Place the four input datasets in the `data/` directory, or update the notebook's input paths to match your local file locations.
3. Update the notebook's data-loading cells to use relative paths (for example, `../data/EdGap_data.xlsx` when running from the `code/` directory) rather than machine-specific absolute paths.
4. Install the requirements.
5. Open and run `education.ipynb` from top to bottom.

The notebook writes the cleaned analysis dataset to `education_clean.csv` in the current working directory. The notebook should be rerun to reproduce the cleaning, visualizations, and model comparisons.

## Communication Document

The project communication document should be included in the [`reports/`](reports/) directory and linked here once its filename is finalized. It should summarize the research question, data, key findings, limitations, and implications for a general audience.

## Ethics and Limitations

- The analysis uses school-level and community-level public data rather than individual student records.
- Community-level measures do not necessarily describe individual students or every part of a school's attendance area.
- Missing predictor values are imputed; results may depend partly on this imputation method.
- State-level differences in ACT participation and reporting can affect comparisons between schools. The notebook includes a robustness model controlling for state and charter status.
- Regression results identify associations and predictive relationships, not causal effects.
- Review data quality, missingness, and model assumptions before drawing policy conclusions.

## Author

**Anush Kallugadde(anush901)**

## License

This project is shared under the **MIT License**. Include a `LICENSE` file containing the standard MIT License text before distributing the repository under this license.

## Acknowledgements

- EdGap.org and the DATA 5100 course repository for the school ACT and socioeconomic dataset.
- The National Center for Education Statistics for the Common Core of Data school, membership, and staff datasets.
- The developers and maintainers of Python, pandas, NumPy, Matplotlib, seaborn, scikit-learn, statsmodels, and Jupyter.
