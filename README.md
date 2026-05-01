# 🏥 NHANES Public Health Data Analysis

![R](https://img.shields.io/badge/Language-R-276DC3?style=flat&logo=r&logoColor=white)
![RMarkdown](https://img.shields.io/badge/Report-RMarkdown-blue?style=flat)
![HackBio](https://img.shields.io/badge/HackBio-Internship%202025-orange?style=flat)
![License](https://img.shields.io/badge/License-MIT-green?style=flat)

---

## 📌 Project Overview

This project is part of the **HackBio Internship 2025 — Task Code 2.7: Public Health**.

**NHANES** (National Health and Nutrition Examination Survey) is a program run by the **CDC** to assess the health and nutritional status of adults and children in the United States. It combines:

- 📋 Survey questions
- 🩺 Physical examinations
- 🔬 Medical, physiological measurements and laboratory tests

A representative sample of approximately **5,000 people per year** is examined. The data supports:

- Determination of disease prevalence and risk factors
- Establishment of national health standards
- Epidemiology studies and public health policy
- Design of health programs and services

---

## 📂 Dataset

| Resource | Link |
|---|---|
| 📊 Main Dataset | [nhanes.csv](https://raw.githubusercontent.com/HackBio-Internship/public_datasets/main/R/nhanes.csv) |
| 📖 Data Dictionary | [nhanes_dd.csv](https://github.com/HackBio-Internship/public_datasets/blob/main/R/nhanes_dd.csv) |

The dataset contains **5,000 participants** and **32 variables** covering demographics, physical measurements, laboratory results, and lifestyle indicators.

---

## 🗂️ Repository Structure

```
📦 nhanes-public-health/
├── 📄 script.Rmd           # Main R Markdown analysis script
├── 📄 README.md            # Project documentation (this file)
├── 📄 nhanes_cleaned.csv   # Cleaned dataset (exported after NA processing)
├── 🖼️ nhanes_histograms.png # Distribution plots (BMI, Weight, Age)
└── 🖼️ nhanes_scatter.png   # Weight vs Height scatter plots
```

---

## ✅ Tasks Completed

### 1. 📦 Package Loading
Packages used: `tidyverse`, `ggplot2`, `patchwork`, `scales`  
Managed via `pacman::p_load()` for automatic installation and loading.

---

### 2. 📥 Data Loading & Data Dictionary
- Dataset loaded directly from GitHub URL
- A **data dictionary object** (`nhanes_dict`) was created with all 32 variable names, types and full labels
- A reusable `get_label()` lookup function allows retrieving any variable's description by name

---

### 3. 🔍 Quick Data Overview
Performed using:
- `str()` — column types and structure
- `unique()` — distinct categories per variable (data quality check)
- `dim()`, `glimpse()`, `summary()` — dimensions and statistics
- Custom `na_summary` table showing missing count and percentage per column

**Key finding:** Most NAs are **structurally expected** — they apply to specific subgroups (e.g. `DiabetesAge` only for diabetics, `nPregnancies` only for females).

---

### 4. 🧹 Missing Value Processing

A **column-by-column strategy** was applied based on the biological meaning of each variable:

| Variable Group | Strategy | Justification |
|---|---|---|
| `HomeRooms`, `HomeOwn` | → `"Unknown"` (factor) | Participants refused to answer |
| `Education`, `MaritalStatus`, `RelationshipStatus`, `Work` | → `"Unknown"` (factor) | Not applicable to young children |
| `BMI`, `Weight`, `Height`, `Pulse`, `BPSys`, `BPDia` | → `0` | Physical exams not completed (mainly infants) |
| `Income`, `Poverty` | → `0` | Non-working participants / refused to answer |
| `HDLChol`, `TotChol`, `Testosterone` | → `median` | Lab tests not performed; median imputation preserves distribution |
| `Diabetes` | → `"Unknown"` (factor) | Not diagnosed / not answered |
| `DiabetesAge` | → `0` | Only meaningful for diabetic participants |
| `nPregnancies`, `nBabies` | → `0` | Males and non-pregnant females |
| `SleepHrsNight`, `PhysActiveDays` | → `0` | Not answered / not applicable |
| `AlcoholDay`, `AlcoholYear` | → `0` | Non-drinkers consumed 0 |
| `SmokingStatus` | → `"Unknown"` (factor) | Not answered |

A helper function `filter_na()` was defined for reproducible exploration of missing rows.

---

### 5. 📊 Histograms — Distribution of Key Variables

Four histograms generated and combined using `patchwork`:

| Plot | Variable | Color |
|---|---|---|
| H1 | BMI (kg/m²) | 🔵 Blue |
| H2 | Weight (kg) | 🟢 Green |
| H3 | Weight (lbs = kg × 2.2) | 🟠 Orange |
| H4 | Age (years) | 🟣 Purple |

![Histograms](nhanes_histograms.png)

---

### 6. 📈 Descriptive Statistics

| Statistic | Result |
|---|---|
| Mean 60-sec pulse rate | **73.63 bpm** |
| Diastolic BP range | **0 – 116 mmHg** |
| Income variance | computed dynamically |
| Income std deviation | computed dynamically |

---

### 7. 🔵 Scatter Plots — Weight vs Height

Three scatter plots visualising the relationship between `Weight` and `Height`, each colored by a different grouping variable:

| Plot | Color Variable |
|---|---|
| S1 | Gender (pink = female, blue = male) |
| S2 | Diabetes status (red = Yes, green = No) |
| S3 | Smoking status (Never / Former / Current) |

![Scatter Plots](nhanes_scatter.png)

---

### 8. 📐 T-Tests & Statistical Conclusions

Three two-sample Welch t-tests were conducted:

#### 7.1 — Age vs Gender
```
H0: No difference in mean age between males and females
H1: Significant difference in mean age between males and females
```

#### 7.2 — BMI vs Diabetes
```
H0: No difference in BMI between diabetic and non-diabetic participants
H1: Significantly higher BMI in diabetic participants
```
> Expected result: **p < 0.05** — diabetes is strongly associated with higher BMI.

#### 7.3 — AlcoholYear vs Relationship Status
```
H0: No difference in yearly alcohol consumption between relationship status groups
H1: Significant difference in yearly alcohol consumption between groups
```
> Compares the two most frequent relationship status groups using `top2[1]` vs `top2[3]`.

---

## 🔧 How to Run

### Prerequisites
Make sure you have **R** (≥ 4.0) and **RStudio** installed.

### Steps

```r
# 1. Clone or download this repository
# 2. Open script.Rmd in RStudio
# 3. Install pacman if not already installed
install.packages("pacman")

# 4. Click "Knit" in RStudio to generate the full HTML/PDF report
# OR run chunk by chunk interactively
```

All packages are installed automatically via `pacman::p_load()` — no manual installation needed.

---

## 📦 Dependencies

| Package | Purpose |
|---|---|
| `tidyverse` | Data manipulation (`dplyr`, `tidyr`) + `ggplot2` |
| `ggplot2` | All visualisations |
| `patchwork` | Combining multiple plots into panels |
| `scales` | Axis label formatting |
| `forcats` | Factor-level NA handling (`fct_explicit_na`) |

---

## 👤 Author

**Ange Maxime TCHOUTANG**  
HackBio Internship 2025 — Task Code 2.7: Public Health

---

## 📜 License

This project is licensed under the **MIT License** — see the [LICENSE](LICENSE) file for details.

---

## 🔗 References

- [CDC NHANES Program](https://www.cdc.gov/nchs/nhanes/)
- [HackBio Public Datasets Repository](https://github.com/HackBio-Internship/public_datasets)
- Turner, S. (2015). *DESeq2 RNA-seq example results* — GitHub Gist
