# Road Accident Severity Analysis

A data science project that performs exploratory data analysis (EDA), data cleaning, outlier treatment, and visualization on the **US Accidents (March 2023)** dataset containing over **7.7 million** accident records across 46 features.

---

## Table of Contents

- [Overview](#overview)
- [Dataset](#dataset)
- [Project Workflow](#project-workflow)
  - [1. Data Loading and Exploration](#1-data-loading-and-exploration)
  - [2. Missing Value Analysis](#2-missing-value-analysis)
  - [3. Data Cleaning](#3-data-cleaning)
  - [4. Outlier Detection and Removal](#4-outlier-detection-and-removal)
  - [5. Correlation Analysis](#5-correlation-analysis)
  - [6. Data Visualization](#6-data-visualization)
- [Technologies Used](#technologies-used)
- [Getting Started](#getting-started)
- [Project Structure](#project-structure)
- [Key Findings](#key-findings)
- [License](#license)

---

## Overview

This project analyzes US traffic accident data to understand the factors influencing accident severity. The analysis pipeline covers data exploration, systematic handling of missing values, outlier removal using statistical methods (Z-Score and IQR), feature reduction through correlation analysis, and a series of visualizations that reveal patterns in accident severity, geography, weather, and time of day.

---

## Dataset

| Property       | Detail                                           |
|----------------|--------------------------------------------------|
| **Source**      | US Accidents (March 2023)                        |
| **File**        | `US_Accidents_March23.csv`                       |
| **Records**     | 7,728,394                                        |
| **Features**    | 46 columns                                       |
| **Coverage**    | 49 US states                                     |
| **Time Span**   | February 2016 -- March 2023                      |

### Feature Categories

| Category                | Features                                                                                                    |
|-------------------------|-------------------------------------------------------------------------------------------------------------|
| **Identifiers**         | ID, Source                                                                                                  |
| **Severity**            | Severity (1--4 scale)                                                                                       |
| **Location**            | Start_Lat, Start_Lng, End_Lat, End_Lng, Street, City, County, State, Zipcode, Country                      |
| **Time**                | Start_Time, End_Time, Timezone                                                                              |
| **Weather**             | Temperature(F), Wind_Chill(F), Humidity(%), Pressure(in), Visibility(mi), Wind_Direction, Wind_Speed(mph), Precipitation(in), Weather_Condition |
| **Road Features**       | Amenity, Bump, Crossing, Give_Way, Junction, No_Exit, Railway, Roundabout, Station, Stop, Traffic_Calming, Traffic_Signal, Turning_Loop |
| **Daylight Indicators** | Sunrise_Sunset, Civil_Twilight, Nautical_Twilight, Astronomical_Twilight                                    |

---

## Project Workflow

### 1. Data Loading and Exploration

- Load the dataset using Pandas
- Preview the first and last records (`head`, `tail`)
- Generate summary statistics (`describe`)
- Inspect data types and memory usage (`info`)
- Check dataset dimensions (`shape`) and column names

### 2. Missing Value Analysis

- Compute null counts per column
- Visualize missing values with:
  - Horizontal bar chart of null counts per column
  - Heatmap of missing values on sampled data (1,000 rows)

### 3. Data Cleaning

**Columns Dropped:**
- `End_Lat`, `End_Lng` -- high null count (~3.4M missing)
- `Airport_Code`, `Weather_Timestamp` -- not relevant to severity analysis
- `Description`, `Street`, `Zipcode`, `ID`, `County`, `Turning_Loop` -- identifiers or low-value features
- `Civil_Twilight`, `Nautical_Twilight`, `Astronomical_Twilight` -- redundant with `Sunrise_Sunset`

**Missing Value Imputation Strategies:**

| Column              | Strategy                                       |
|---------------------|------------------------------------------------|
| City                | Random fill from low-frequency cities          |
| Wind_Chill(F)       | Median imputation                              |
| Temperature(F)      | Median imputation                              |
| Wind_Direction      | Mode imputation (invalid values set to NaN first) |
| Weather_Condition   | Filled with `'Fair'`                           |
| Humidity(%)         | Group-wise median by State                     |
| Visibility(mi)      | Group-wise median by Weather_Condition          |
| Wind_Speed(mph)     | Filled with constant `6.8`                     |
| Timezone            | Group-wise mode by State                       |
| Pressure(in)        | Group-wise median by State                     |
| Precipitation(in)   | Filled with `0` (clear weather assumption)     |
| Sunrise_Sunset      | Mode imputation                                |

### 4. Outlier Detection and Removal

Three-stage outlier removal pipeline:

1. **Z-Score Filtering** (threshold = 5)
   - Applied to: `Temperature(F)`, `Wind_Chill(F)`, `Pressure(in)`, `Wind_Speed(mph)`
   - Result: 7,728,394 → 7,654,777 rows

2. **IQR Filtering** (1.5 * IQR)
   - Applied to: `Distance(mi)`, `Visibility(mi)`, `Precipitation(in)`, `Humidity(%)`
   - Result: 7,654,777 → 5,313,815 rows

3. **Sanity Threshold Filtering**
   - Wind_Speed < 200 mph
   - Temperature > -100 F
   - Visibility < 200 mi

4. **Duplicate Removal**
   - 76,643 duplicate rows removed
   - Final dataset: **5,237,172 rows x 32 columns**

### 5. Correlation Analysis

- Generated correlation heatmaps before and after feature reduction
- Identified and removed `Wind_Chill(F)` due to high correlation with `Temperature(F)`
- Validated with a post-removal correlation heatmap

### 6. Data Visualization

| Visualization                        | Type          | Purpose                                        |
|--------------------------------------|---------------|------------------------------------------------|
| Null values bar chart                | Bar (H)       | Identify columns with missing data             |
| Missing values heatmap               | Heatmap       | Visualize null patterns across sampled rows     |
| Severity distribution                | Count plot    | Understand target variable balance              |
| Severity vs Visibility               | Box plot      | Examine visibility impact on severity           |
| Day vs Night severity                | Count plot    | Compare severity across daylight conditions     |
| Wind speed distribution              | Histogram     | Inspect weather feature distribution            |
| Geographic accident hotspots         | Scatter plot  | Map accident locations by severity              |
| Feature correlation matrix           | Heatmap       | Identify multicollinearity                      |
| Temperature density distribution     | KDE plot      | Smooth distribution view of temperature         |
| Box plots (post-cleaning)            | Box plot grid | Validate outlier removal effectiveness          |

---

## Technologies Used

| Library        | Version | Purpose                                    |
|----------------|---------|--------------------------------------------|
| Python         | 3.x     | Programming language                       |
| NumPy          | --      | Numerical computations                     |
| Pandas         | --      | Data manipulation and analysis             |
| Matplotlib     | --      | Static visualizations                      |
| Seaborn        | --      | Statistical visualizations                 |
| SciPy          | --      | Z-score computation for outlier detection  |

---

## Getting Started

### Prerequisites

```bash
pip install numpy pandas matplotlib seaborn scipy
```

### Running the Notebook

1. Clone the repository:
   ```bash
   git clone https://github.com/<your-username>/ROAD_ACCIDENT_SEVERITY_ANALYSIS.git
   cd ROAD_ACCIDENT_SEVERITY_ANALYSIS
   ```

2. Place the dataset file `US_Accidents_March23.csv` in the project root directory.

3. Open and run the notebook:
   ```bash
   jupyter notebook Week-3.ipynb
   ```

> **Note:** The dataset file path in the notebook is set to `C:\ROAD_ACCIDENT_SEVERITY_ANALYSIS\US_Accidents_March23.csv`. Update this path if your project directory differs.

---

## Project Structure

```
ROAD_ACCIDENT_SEVERITY_ANALYSIS/
├── Week-3.ipynb                 # Main analysis notebook
├── US_Accidents_March23.csv     # Dataset (not included in repo)
└── README.md                    # Project documentation
```

---

## Key Findings

- **Severity 2** dominates the dataset, accounting for ~80% of all records
- **Temperature** and **Wind Chill** are highly correlated (r > 0.9), making one of them redundant
- Accidents are significantly more frequent during **daytime** compared to nighttime
- **Geographic clustering** of accidents is concentrated along major highway corridors and urban centers (California, Texas, Florida)
- Most weather-related features (humidity, visibility, precipitation) show weak direct correlation with severity, suggesting severity depends more on road and traffic conditions

---

## License

This project is for educational and research purposes. The dataset is sourced from publicly available US accident records.
