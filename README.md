# Titanic Data Cleaning (Excel)
An end-to-end data cleaning project using Microsoft Excel on a real-world raw dataset.

## Table OF Contents
- [Project Overview](#project-overview)
- [Problem Statement](#problem-statement)
- [Objective](#objective)
- [Dataset Description](#dataset-description)
- [Data Cleaning and Preparation](#data-cleaning-and-preparation)
- [Challenges Encountered](#challenges-encountered)
- [Continuous Data Quality Improvement](#continuous-data-quality-improvement)
- [Conclusion](#conclusion)
- [Tools Used](#tools-used)




---
## Project Overview
This project focuses on the end-to-end data cleaning and preprocessing of the Titanic: Machine Learning from Disaster dataset sourced from Kaggle. It contains information about passengers who were aboard the Titanic, including personal details, ticket information, and travel class. The primary goal was to transform raw, inconsistent data into a structured, analysis-ready format suitable for statistical modeling.


---
## Problem Statement
Raw datasets are rarely ready to use without preparation. When first reviewed, the Titanic dataset showed several issues that would make analysis unreliable or misleading if they were not addressed. These included missing age and cabin information, inconsistent name structures, mixed ticket formats, and categorical values that were not standardized.
If these issues were left unresolved, any analysis carried out on the dataset could lead to inaccurate results or incorrect conclusions. The challenge, therefore, was to clean the data in a way that improved consistency and usability while still respecting the original information.

Research Question: How can a messy, real-world dataset be cleaned and organized in Excel without guessing missing values or unnecessarily removing data?


---
## Objective
  -	 Understand the structure and quality of a raw dataset
  -	 Identify and fix common data quality issues
  -	 Handle missing values in a sensible and transparent way
  -	 Standardize names, categories, and formats
  -	 Improve column clarity through renaming and restructuring
  -	 Produce a clean dataset that is ready for further analysis


---
### Dataset Description
The dataset used in this project is the Titanic passenger dataset, obtained from Kaggle.
It contains demographic and travel information for passengers aboard the RMS Titanic and is commonly used for data quality and preprocessing practice.
 The dataset contains 891 rows of passenger data and 12 (original) columns present (13 after cleaning, with the addition of the Cabin_Indicator column). The row count remained unchanged throughout the cleaning process to avoid data loss.
 
    • Key Fields

| Column               | Description                                              |
|----------------------|-----------------------------------------------           |
| PassengerId          | Unique passenger identifier                              |
| Survived             | Survival status (0 = No, 1 = Yes)                        |
| Pclass               | Passenger class (1st, 2nd, 3rd)                          |
| Name                 | Full passenger name (raw)                                |
| Title                | Extracted and standardized title (new column)            |
| Sex                  | Gender of passenger                                      |
| Age                  | Age in years (imputed where missing)                     |
| SibSp                | Number of siblings/spouses aboard                        |
| Parch                | Number of parents/children aboard                        |
| Ticket               | Ticket number (preserved)                                |
| Fare                 | Fare paid (median-imputed where missing)                 |
| Cabin                | Cabin number (mostly missing)                            |
| Cabin_Indicator      | Binary: 1 = cabin present, 0 = cabin absent (new column) |
| Embarked             | Port of embarkation (C, Q, S)                            |


---
### Initial Data Quality Observations
| Column	               | Issue Found                                                                                |
|-----------------------|------------------------------------------------------------------------------------------- |
| Age	                  | 177 missing values (19.9% of records)                                                      |
| Fare	                 | 2 missing values (0.2% of records)                                                         |
| Cabin	                | 687 missing values (77.1% of records)                                                      |
| Name                  | Contains nicknames in parentheses, quotation marks, and inconsistent punctuation           |
| Ticket                |	Mixed alphanumeric formats with no consistent structure                                    |
| Embarked	             | Minor inconsistencies in categorical labels                                                |


---
### Data Cleaning and Preparation
Before analysis, the dataset was reviewed and prepared to ensure accuracy and consistency. All cleaning decisions were justified, minimal and non-destructive. 

Cleaning steps performed:
1.	Handling Missing Values
   
   - Age
Missing values in the Age column were identified. To maintain data integrity without removing rows, Mean Imputation was applied.

| Metric	             |   Value            |
| --------------------|--------------------|
| Total records	      |    891             |
| Missing Age values	 |    177             |
| Percentage affected	|    19.9%           |
| Imputation method	  |    Mean Imputation |

    Age Imputation Decision
Mean imputation was applied to 177  of 891 age records (19.9%), ensuring a complete feature set for survival prediction modeling. This approach was selected because age is a continuous variable and the proportion of missing values was moderate. Removing these records would have resulted in unnecessary data loss. 

Formula applied across all 891 rows:
=IF(ISBLANK(G2),  AVERAGE($G$2:$G$892), G2)

 - Fare
 Fare Imputation Decision
Only 2 of 891 fare records (0.2%) contained missing values- a negligible proportion that posed minimal risk of introducing bias. Median imputation was applied rather than mean, as fare values were right-skewed due to extreme outliers among first-class passengers.

|    Metric             |    Value                |
|-----------------------|-------------------------|
| Total records	        |    891                  |
| Missing Fare values   |	   2                    |  
| Percentage affected 	 |    0.2%                 |
| Imputation method	    |    Median Imputation    |

Formula applied:
=IF (ISBLANK (L2), MEDIAN ($L$2:$L$892), L2)

   - Cabin
The cabin column contained a very high proportion of missing values, making imputation impractical and potentially misleading. Rather than attempting to guess cabin assignments, a binary indicator variable (has_cabin) was created to capture whether cabin information was recorded.

| Cabin_Indicator Value	  |    Meaning	             | Record Count    |
|-------------------------|-------------------------|---------------- |
|       1	                |  Cabin value present	   |  204            |
|       0                 |  Cabin value absent     |  missing	687    |

Formula applied to all 891 rows:
=IF(TRIM(M2)="", 0, 1)

Note: TRIM() was used instead of ISBLANK() because some cells appeared blank but contained empty strings, which ISBLANK() would not detect.


2.	Standardization of Formats and Categories
 - Name Cleaning
Passenger names contained inconsistencies including nicknames in parentheses, quotation marks, and excess whitespace. The following transformations were applied:
Removed nicknames and additional text within parentheses
Removed quotation marks and excess whitespace
Preserved the original Last, First Middle naming structure

Before and after cleaning examples:
| PassengerId    |   Original Name (Raw)            |	Cleaned Name
|----------------|----------------------------------|----------------------------|
| 1              |  	Braund, Mr. Owen Harris        |	Braund, Owen Harris        |
| 3	             |   Heikkinen, Miss. Laina         | Heikkinen, Laina           |
| 12	            |   Bonnell, Miss. "Elizabeth"     | 	Bonnell, Elizabeth        |
| 55	            |   Ostby, Mr. Engelhart Cornelius |	Ostby, Engelhart Cornelius |
| 768            | 	 Moran, Mr. James ("Jimmy")     | 	Moran, James              |

- Title Extraction and Standardization
  
Titles were extracted from raw names into a dedicated column using the following formula:
=MID(B2, FIND(",", B2)+2, FIND(".", B2)-FIND(",", B2)-2)
Rare or honorary titles (Col, Rev, Dr, Dona, Capt, Major, etc.) were grouped under the label "Other" to reduce cardinality and prevent sparse categorical values in downstream modeling.

| Original Title | Standardized Category | Example Passenger       |
|---------------|----------------------|-------------------        |
| Miss          |         Miss         |    Heikkinen, Laina       |
| Mrs           |         Mrs          |  Cumings, John Bradley    |
| Master        |         Master       |  Palsson, Gosta Leonard   |
| Dr            |         Other        |    Leader, Alice          |
| Rev           |         Other        | Byles, Thomas Roussel     |
| Col           |         Other        | Gracie, Archibald IV      |
| Dona          |         Other        | de Messemaeker, Guillaume |

 - Ticket Preservation
   
Ticket formats were preserved because the mix of alphanumeric codes may represent boarding groups, agents, or embarkation points. Splitting or modifying these identifiers could lead to loss of contextual meaning or accidental duplication. No cleaning was applied.


3.	Data Type Validation
Ensured all numeric columns (Age, Fare, SIBSp, and Parch) contained only numeric values.
Confirmed all categorical columns (Sex, Embarked, Pclass, and Title) had consistent standardized label.
Ensured no formulas remained in final cleaned columns — all formula outputs were pasted as values
  	

4.	Duplicates
No duplicates were identified no records were removed during the cleaning process


5.	Outliers and Invalid Entries
Fare values were reviewed for extreme outliers. Several first-class passengers paid fares significantly above the median. These were retained rather than removed, as the values are considered historically valid and reflect actual ticket pricing disparities aboard the Titanic.


---
## CHALLENGES ENCOUNTERED
  - Inconsistent Name Formats: Names contained titles, nicknames, punctuation, and mixed patterns.
Cleaned name first, then extracted and standardized titles separately

  - Cells appeared blank but contains empty strings
Used TRIM (cell) ="" instead of ISBLANK

  - 77.1% of Cabin values were missing 
Created a Cabin_Indicator binary column instead of guessing value

  - Deciding what not to change in the case of Ticket ( which has letters and numbers
Preserved data integrity by understanding the context


---
## CONTINUOUS DATA QUALITY IMPROVEMENT
  •	Recommendation for Collection
-	Enforce standardized name and title entry at source using controlled input fields
-	Separate cabin availability from cabin identifier into two distinct columns
-	Implement mandatory field completion for age and fare at data entry stage

  •	Validation Checks
-	Drop-down lists for categorical entries (Sex, Embarked, Pclass, Title)
-	Duplicate detection rules with context awareness
-	Range check for numerical values( e.g., Age: 0-120, Fare: 0-600)

  •	Suggested Automation
- Use Excel Power Query to automate the Ticket-splitting process should domain confirmation be obtained in future iterations. Power Query can also automate title extraction and name standardization for new data loads.


 ---
### CONCLUSION
This project demonstrates a complete, methodical data-cleaning workflow using Excel on a real-world dataset. All changes were justified, minimal, and non-destructive, preserving data integrity while significantly improving usability and quality.
Key outcomes of this cleaning process:

  - 177 missing Age values (19.9%) were resolved through mean imputation, ensuring a complete feature set for survival prediction modeling
  -  2 missing Fare values (0.2%) were resolved through median imputation, appropriate given the right-skewed fare distribution
  -  The Cabin_Indicator binary column was created, allowing analysts to test the hypothesis that passengers with assigned cabins had a higher survival probability than those without — serving as a proxy for socioeconomic status and proximity to lifeboats. This derived feature adds analytical value without altering or fabricating the original cabin data
  - Name standardization and title extraction improved  readability and created a new categorical variable suitable for modeling
  - The Ticket column was preserved as-is to protect potentially meaningful alphanumeric group codes.
The cleaned dataset is now ready for exploratory data analysis, feature engineering, and machine learning model development.


---
### Tools Used
Microsoft Excel [Data cleaning, imputation, formula logic, and formatting]




 







