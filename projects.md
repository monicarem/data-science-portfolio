# Projects

I will be adding my data science projects here throughout the semester.

---

## Project 1: Fast Food Density and Health Outcomes in U.S. Counties
## Research Question

Is there a correlation between the number of fast food restaurants per capita and obesity rates across U.S. counties?

### Context
Obesity and diabetes are major public health concerns in the United States, and many people assume that living near more fast food restaurants leads to worse health outcomes. But is that actually true at the county level? This project explores whether fast food density predicts obesity and diabetes, or whether other factors like poverty and income matter more.
- **USDA Food Environment Atlas** - county-level data on fast food restaurant density, median household income, and poverty rate (downloaded as Excel file)
- **CDC PLACES API** - county-level obesity and diabetes prevalence estimates from the BRFSS survey, pulled live via the Socrata API at `data.cdc.gov` (dataset ID: `swc5-untb`)

### Variables

| Variable | Source | Role |
|----------|--------|------|
| Fast food restaurant per 1,000 people | USDA | Independent |
| Obesity Prevalence | CDC | Dependent |
| Diabetes Prevalence | CDC | Dependent |
| Median Household Income | USDA | Control |
| Poverty Rate | USDA | Control |

## Data Cleaning

- Loaded USDA sheets with `header=1` because row 0 contained sheet labels, not column names
- Replaced USDA missing value codes (-8888 and -9999) with NaN and dropped incomplete rows
- Filtered CDC data to crude prevalence only and to 5-digit FIPS codes (county level) to avoid duplicate estimates
- Merged all sources on FIPS code, resulting in 2,468 clean counties

## Key Findings

| Relationship | Correlation |
|--------------|-------------|
| Fast food density vs. obesity | -0.199 |
| Poverty rate vs. obesity | 0.491 |
| Median income vs. obesity | -0.586 |
| Fast food density vs. diabetes | -0.127 |
| Poverty rates vs. diabetes | 0.737 |

Fast food density actually shows a weak correlation with obesity and diabetes. Counties with more fast food per capita tend to be slightly healthier. Poverty and income, on the other hand, show much stronger relationships with health outcomes. Poverty rate alone correlates at 0.737 with diabetes.

### Visualizations

**Chart 1: Fast Food Density vs. Adult Obesity Rate**

<img width="989" height="590" alt="image" src="https://github.com/user-attachments/assets/4f61efea-bc9d-4dda-9570-f0f2632ef268" />
This scatter plot shows the weak negative relationship between fast food density and obesity. The downward trend line suggests that fast food density alone does not explain obesity patterns.


**Chart 2: Poverty Rate vs. Adult Obesity Rate**

<img width="990" height="590" alt="image" src="https://github.com/user-attachments/assets/2081f0b9-2a43-41e0-8ec8-8ca7f5d2e29e" />
This scatter plot shows the much stronger positive relationship between poverty and obesity. The upward trend is clear and consistent across counties.

### Ethics and Limitations

- **Ecological fallacy**: County-level data cannot tell us about individual behavior. A county with high fast food density is not necessarily one where residents eat more fast food.
- **Missing data**: 675 counties were dropped due to missing values, which may bias results toward counties with more complete reporting.
- **Confounding**: Fast food density correlates with urbanization and income, which are independently linked to health.
- **Causality**: This analysis shows correlation only. It does not prove that fast food causes or prevents obesity.
- **Data vintage**: Fast food data is from 2020, health data from 2023, and income data from 2021. Relationships may have shifted since.

### Code

Full notebook available here: 

### AI Usage Disclosure

AI tools were used to assist with debugging API calls, cleaning code structure, and providing feedback on the write-up. All analysis, interpretation, and final code decisions were made by the author.
