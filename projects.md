# Projects

Coming soon! I will be adding my data science projects here throughout the semester.

---

## Project 1: Fast Food Density and Health Outcomes in U.S. Counties
## Research Question

Is there a correlation between the number of fast food restaurants per capita and obesity rates across U.S. counties?

### Context
Obesity and diabetes are major public health concerns in the United States, and many people assume that living near more fast food restaurants leads to worse health outcomes. But is that actually true at the county level? This project explores whether fast food density predicts obesity and diabetes, or whether other factors like poverty and income matter more.
- **USDA Food Environment Atlas** - county-level data on fast food restaurant density, median household income, and poverty rate (downloaded as Excel file)
- **CDC PLACES API** - county-level obesity and diabetes prevalence estimates from the BRFSS survey, pulled live via the Socrata API at `data.cdc.gov` (dataset ID: `swc5-untb`)

### Variables

Variable | Source | Role
Fast food restaurant per 1,000 people | USDA | Independent
Obesity Prevalence | CDC | Dependent
Diabetes Prevalence | CDC | Dependent
Median Household Income | USDA | Control
Poverty Rate | USDA | Control
