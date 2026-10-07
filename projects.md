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

Full notebook available here: health analysis.ipynb

### AI Usage Disclosure

AI tools were used to assist with debugging API calls, cleaning code structure, and providing feedback on the write-up. All analysis, interpretation, and final code decisions were made by the author.

## Project 2: Predicting High-Obesity Counties from Food Environment and Socioeconomic Data

## Research Question

Can we predict whether a U.S. county has above-median adult obesity prevalence using food environment, income, poverty, and health data?

## 1. Problem Definition

**Prediction Problem:** Predicting whether a U.S. county will fall into the high-obesity category, meaning its adult obesity rate is above the national median, based on food environment, socioeconomic, and health-related features from public county-level datasets.

**Target Variable:** `High_Obesity`. This is a binary variable where 1 means the county's adult obesity prevalence is above 38.0%, and 0 means it is at or below 38.0%. The 38.0% cutoff comes from the median of the CDC PLACES crude obesity prevalence values after cleaning the data.

**Problem Type:** Classification. The target variable has two possible outcomes, high obesity or not high obesity. Because of this, I will use classification models such as logistic regression, decision trees, and ensemble methods. I will evaluate the models using accuracy, precision, recall, F1, and ROC-AUC.

**Who Benefits:**
- County and state public health departments deciding where to focus obesity prevention and nutrition programs
- Community health centers and nonprofits working on food access and health programs
- Healthcare systems planning for chronic disease care in different regions
- Policymakers deciding whether socioeconomic factors or the food environment should be considered when planning interventions

**Why It Matters:** Adult obesity affects more than 40% of U.S. adults and is linked to diabetes, heart disease, and other health problems. These conditions also create high healthcare costs. Obesity rates are not the same across all counties, and some areas have much higher rates than others. Since public health resources are limited, being able to identify counties that may have higher obesity rates could help health departments decide where to focus their resources.

Project 1 showed that socioeconomic factors had stronger relationships with obesity than fast food density. Poverty had a correlation of r = 0.491, while median income had a correlation of r = -0.586. Fast food density had a much weaker correlation of r = -0.199. This project builds on those findings by moving from looking at relationships to making predictions. Instead of only asking whether obesity is related to poverty, I want to see whether a county's food environment, income, poverty, and diabetes prevalence can predict whether it falls into the high-obesity group. If the model performs well, it could help identify counties that may need more attention. If it does not perform well, or if diabetes is the main feature driving the predictions, that can also show the limits of using county-level data to predict obesity.

## 2. Background and Context

Obesity is one of the major public health challenges in the United States. According to the National Center for Health Statistics, 40.3% of U.S. adults were classified as obese during the 2021–2023 period, while another 9.4% were classified as severely obese (Emmerich et al., 2024). Obesity can also increase the risk of health problems such as type 2 diabetes, cardiovascular disease, hypertension, and several types of cancer. The healthcare costs related to obesity are also high. Adults with obesity-related multimorbidity have higher healthcare use and costs, with costs that are 151% to 264% higher than for adults without these conditions (Ezendu et al., 2025). These costs also vary across the country. Obesity rates can differ based on factors such as location, income, and access to healthy food, which makes county-level prediction useful for understanding where obesity rates may be higher.

Previous research has looked at why obesity rates are higher in some areas than others. One recent study of more than 3,000 U.S. counties found that food insecurity, poverty, unemployment, limited access to healthy food retailers, and higher densities of fast-food and convenience stores were associated with higher adult obesity rates (Abraham et al., 2026). The study also found that higher median household income and greater access to recreational facilities were associated with lower obesity rates (Abraham et al., 2026). These findings are similar to what Project 1 found at the bivariate level. Across 2,468 counties, poverty had a correlation of r = 0.491 with obesity, while median income had a correlation of r = -0.586. Fast-food density had a weaker relationship with obesity, with a correlation of r = -0.199. This suggests that socioeconomic factors may be more useful for predicting obesity than food environment variables alone. This is something that can be tested further with a machine learning model.

The main focus of this project is to move from describing relationships to making predictions. Previous research has mostly focused on identifying factors that are associated with obesity. This project looks at whether those same factors can predict which counties will fall into the high-obesity category. It also tests whether adding diabetes prevalence improves the model's predictions. To do this, the project compares a model that uses food environment and socioeconomic features with a model that also includes diabetes prevalence. This can help show whether structural factors or health-related factors are more useful for predicting county-level obesity.

### References

Emmerich SD, Fryar CD, Stierman B, Ogden CL. Obesity and severe obesity prevalence in adults: United States, August 2021–August 2023. NCHS Data Brief, no 508. Hyattsville, MD: National Center for Health Statistics. 2024. DOI: https://dx.doi.org/10.15620/cdc/159281
Abraham, A. M., Swartz, M. D., van den Berg, A. E., & Linder, S. H. (2026). Multilevel Analysis of the Food and Physical Activity Environment and Adult Obesity Across U.S. Counties and States. International journal of environmental research and public health, 23(2), 142. https://doi.org/10.3390/ijerph23020142Ezendu, K., Pohl, G., Lee, C. J., Wang, H., Li, X., & Dunn, J. P. (2025). Prevalence of obesity-related multimorbidity and its health care costs among adults in the United States. Journal of managed care & specialty pharmacy, 31(2), 179–188. https://doi.org/10.18553/jmcp.2025.31.2.179

## 3. Data Description

This project uses data from two public sources: the USDA Food Environment Atlas and the CDC PLACES program. Both are free, county-level datasets that are commonly used in public health research.

### USDA Food Environment Atlas

The USDA Food Environment Atlas has county-level data on food access, restaurants, and socioeconomic conditions. I used two sheets from the 2025 release:

- **RESTAURANTS** — fast food restaurants per 1,000 people (`FFRPTH20`, from 2020)
- **SOCIOECONOMIC** — median household income (`MEDHHINC21`, from 2021) and poverty rate (`POVRATE21`, from 2021)

Each sheet has about 3,144 rows — one per U.S. county. One thing to note: the first row in each sheet is a label row, not column names, so I had to load the files with `header=1`.

**Source:** U.S. Department of Agriculture Economic Research Service. (2025). *Food Environment Atlas*. https://www.ers.usda.gov/data-products/food-environment-atlas/

### CDC PLACES

The CDC PLACES program provides county-level estimates of chronic disease prevalence, based on the BRFSS survey. I pulled two measures live from the Socrata API at `data.cdc.gov` (dataset ID `swc5-untb`):

- **OBESITY** — adult obesity crude prevalence (`CDC_OBESITY`, from 2023)
- **DIABETES** — adult diabetes crude prevalence (`CDC_DIABETES`, from 2023)

I kept only crude prevalence values (not age-adjusted) so I wasn't mixing two different types of estimates. The API returned about 5,900 records per measure, all from the 2023 release.

**Source:** Centers for Disease Control and Prevention. (2023). *PLACES: Local data for better health*. U.S. Department of Health and Human Services. https://www.cdc.gov/places

### Rows, Size, and Merge

Each row in the final dataset is **one U.S. county**. I merged the USDA and CDC data on the **5-digit FIPS county code**.

After cleaning, the final dataset has **2,468 counties and 8 columns**:

| Column | Description | Role |
|---|---|---|
| `FIPS` | 5-digit county code | Key |
| `State` | State abbreviation | Identifier |
| `County` | County name | Identifier |
| `FFRPTH20` | Fast food restaurants per 1,000 people (2020) | Feature |
| `MEDHHINC21` | Median household income (2021) | Feature |
| `POVRATE21` | Poverty rate (2021) | Feature |
| `CDC_OBESITY` | Adult obesity rate, % (2023) | **Target** |
| `CDC_DIABETES` | Adult diabetes rate, % (2023) | Feature (Model 2 only) |

### Missing Data

Both sources use `-8888` and `-9999` as missing-value codes. I replaced those with `NaN` and dropped any row with missing values. That dropped the dataset from 3,144 counties down to 2,468 — about 78.5% of U.S. counties.

### Assumptions and Limitations

- **Different years across sources.** The USDA data is from 2020 (food) and 2021 (income/poverty), while the CDC data is from 2023. This 2–3 year gap is normal for combining public datasets, and structural things like fast food density and median income don't change fast, so the mismatch probably doesn't hurt the model much. Still worth mentioning.
- **CDC PLACES values are modeled, not measured.** The obesity and diabetes numbers are statistical estimates from BRFSS survey data, not direct measurements of every resident.
- **County-level data can't describe individuals.** A county with high predicted obesity doesn't mean any specific person there is obese.
- **Lost about 21% of counties.** Counties with missing or suppressed data were dropped, which likely biases results toward larger, more urban counties that report more completely.
- **Data vintage.** The model reflects the 2020–2023 window. Predictions shouldn't be assumed to hold for later years without re-training.

## 4. Data Understanding and Exploration

Before modeling, I explored the cleaned dataset to understand the target variable, check for class balance, look at feature distributions, and see which features correlate most with obesity.

### Creating the Binary Target

I converted `CDC_OBESITY` into a binary target called `High_Obesity` using the national median of 38.0% as the cutoff:

```python
df["High_Obesity"] = (df["CDC_OBESITY"] > 38.0).astype(int)
```

## 5. Data Preparation and Feature Selection

### Missing Values

Both source datasets use `-8888` and `-9999` as missing-value codes. I replaced those with `NaN` and dropped any row with a missing value. After cleaning, the final dataset has 2,468 counties with zero missing values in any column:
FIPS 0
State 0
County 0
FFRPTH20 0
MEDHHINC21 0
POVRATE21 0
CDC_OBESITY 0
CDC_DIABETES 0
High_Obesity 0

### Duplicates

I checked for both duplicate rows and duplicate county IDs. There are no duplicate rows and no duplicate FIPS codes. Each county appears exactly once.

### Feature Selection

I selected four numeric features based on the exploration in Section 4:

| Feature | Role in Model |
|---|---|
| `FFRPTH20` (fast food density) | Feature in Model 1 and Model 2 |
| `MEDHHINC21` (median income) | Feature in Model 1 and Model 2 |
| `POVRATE21` (poverty rate) | Feature in Model 1 and Model 2 |
| `CDC_DIABETES` (diabetes prevalence) | Feature in Model 2 only |

I dropped `State`, `County`, and `FIPS` before modeling because they are identifiers and not predictors. Including them could allow the model to learn specific locations instead of the patterns in the data. All of the features are numeric, so categorical encoding is not needed.

I kept `CDC_DIABETES` separate instead of including it in both models. This allows me to compare two models:

- **Model 1 features:** `FFRPTH20`, `MEDHHINC21`, `POVRATE21` (food environment and socioeconomic factors only)
- **Model 2 features:** Model 1 features plus `CDC_DIABETES`

Model 1 uses food environment and socioeconomic factors, while Model 2 adds diabetes prevalence. The goal is to see whether diabetes adds useful predictive information beyond the other factors.

### Train/Test Split

I used an 80/20 stratified split with `random_state=42. Stratification keeps the proportion of high-obesity and low-obesity counties similar in the training and testing sets.

```python
from sklearn.model_selection import train_test_split

X = df[["FFRPTH20", "MEDHHINC21", "POVRATE21", "CDC_DIABETES"]]
y = df["High_Obesity"]

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, stratify=y, random_state=42
)
```
## 6. Baseline and Model Development

### Baseline

Before training the models, I created a baseline using a majority-class classifier. This model does not use any of the features and predicts the most common class in the training data for every county. Since the two classes are close to a 50/50 split, this gives me a starting point for comparing the other models.

The baseline had an accuracy of 0.506 and an F1 score of 0.000. The F1 score is 0.000 because the model never predicts the high-obesity class. This means the precision and recall for class 1 are both zero. The other models need to perform better than this baseline, especially on F1 and ROC-AUC.

### Models Trained

I trained three classification models using each of the two feature sets, giving me six models in total:

- **Logistic Regression.** A linear model that predicts the probability of each class. It is also useful because the coefficients can be interpreted.
- **Decision Tree.** A model that makes decisions by splitting the data based on feature values. It can capture non-linear relationships and is easy to visualize.
- **Random Forest.** A model that combines multiple decision trees. It can work well with tabular data, but it is harder to interpret than one decision tree.

I chose these three models because they give me different ways to approach the prediction problem. Logistic Regression tests a linear relationship, while the Decision Tree and Random Forest can capture more complex patterns.

### Two Feature Sets

I trained each model twice using two different feature sets:

- **Model 1 features:** FFRPTH20, MEDHHINC21, POVRATE21 (food and socioeconomic features)
- **Model 2 features:** Model 1 features plus CDC_DIABETES

Model 1 uses food environment and socioeconomic features, while Model 2 adds diabetes prevalence. This allows me to see whether adding diabetes improves the model's predictions. I discuss the differences between the two feature sets in Section 9.

### Hyperparameter Tuning

I tuned each model using 5-fold cross-validation on the training data. The test data was not used during tuning so that it could be saved for the final evaluation. This helps prevent data leakage.

Grids used:

- **Logistic Regression:** C in [0.01, 0.1, 1, 10]
- **Decision Tree:** max_depth in [3, 5, 7, None], min_samples_leaf in [1, 5, 10]
- **Random Forest:** n_estimators in [100, 200], max_depth in [5, 10, None]

Best parameters found:

| Model | Best Parameters |
|---|---|
| LogReg Model 1 | C = 10 |
| LogReg Model 2 | C = 10 |
| Tree Model 1 | max_depth = 5, min_samples_leaf = 1 |
| Tree Model 2 | max_depth = 5, min_samples_leaf = 5 |
| RF Model 1 | n_estimators = 200, max_depth = 10 |
| RF Model 2 | n_estimators = 200, max_depth = 5 |

For Logistic Regression, C = 10 was selected for both feature sets. This means the model preferred less regularization within the values I tested. For the Decision Trees, max_depth = 5 was selected for both models, which helped keep the trees from becoming too complex.

### Results

| Model | Accuracy | Precision | Recall | F1 | ROC-AUC |
|---|---|---|---|---|---|
| Baseline (majority) | 0.506 | 0.000 | 0.000 | 0.000 | 0.500 |
| LogReg Model 1 | 0.706 | 0.691 | 0.734 | 0.712 | 0.780 |
| LogReg Model 2 | 0.715 | 0.712 | 0.709 | 0.710 | 0.814 |
| Tree Model 1 | 0.676 | 0.693 | 0.619 | 0.654 | 0.751 |
| Tree Model 2 | 0.725 | 0.703 | 0.766 | 0.733 | 0.805 |
| RF Model 1 | 0.700 | 0.698 | 0.693 | 0.695 | 0.782 |
| RF Model 2 | 0.709 | 0.694 | 0.734 | 0.713 | 0.813 |

All of the trained models performed better than the baseline. Accuracy increased from 0.506 to between 0.676 and 0.725. F1 increased from 0.000 to between 0.654 and 0.733. ROC-AUC also increased from 0.500 to between 0.751 and 0.814. This shows that the features provide useful information for predicting high-obesity counties.

### Fair Comparison

To make the comparison fair, I used the same train/test split for every model, with random_state = 42. I also used the same cross-validation setup when tuning the models. Each model was evaluated using the same test data, so the results can be compared directly.

The main differences between the models are the algorithm and the features being used. The next section looks more closely at the results and explains why I selected the final model.

## 7. Model Evaluation and Selection

### Metrics Used

I evaluated each model using five different metrics. Each metric shows a different part of how well the model performed.

**Accuracy** is the percentage of predictions the model got correct. It is easy to understand, but it can be misleading when the classes are unbalanced. Since the two classes in this dataset are close to 50/50, accuracy is useful for this project.

**Precision** shows how often the model is correct when it predicts that a county has high obesity. A higher precision means fewer false positives.

**Recall** shows how many of the counties that actually have high obesity the model was able to identify. A higher recall means fewer high-obesity counties were missed.

**F1** combines precision and recall into one score. It gives a balance between the two, so both types of errors are taken into account.

**ROC-AUC** measures how well the model separates high-obesity counties from other counties across different thresholds. A higher ROC-AUC means the model is better at separating the two classes.

I used F1 as the main metric during hyperparameter tuning because both false positives and false negatives matter in this project. A false negative means that a high-obesity county is missed, while a false positive means that a county is incorrectly flagged as high obesity. F1 gives a balance between these two types of errors.

### How Each Model Performed

| Model | Accuracy | Precision | Recall | F1 | ROC-AUC |
|---|---|---|---|---|---|
| Baseline (majority) | 0.506 | 0.000 | 0.000 | 0.000 | 0.500 |
| LogReg Model 1 | 0.706 | 0.691 | 0.734 | 0.712 | 0.780 |
| LogReg Model 2 | 0.715 | 0.712 | 0.709 | 0.710 | 0.814 |
| Tree Model 1 | 0.676 | 0.693 | 0.619 | 0.654 | 0.751 |
| Tree Model 2 | 0.725 | 0.703 | 0.766 | 0.733 | 0.805 |
| RF Model 1 | 0.700 | 0.698 | 0.693 | 0.695 | 0.782 |
| RF Model 2 | 0.709 | 0.694 | 0.734 | 0.713 | 0.813 |

All of the trained models performed better than the baseline. Accuracy increased from 0.506 to between 0.676 and 0.725. F1 increased from 0.000 to between 0.654 and 0.733. ROC-AUC also increased from 0.500 to between 0.751 and 0.814. This shows that the features provide useful information for predicting high-obesity counties.

### Model 1 vs. Model 2

Adding diabetes helped every model, but not by much:

| Model Pair | F1 Change | ROC-AUC Change |
|---|---|---|
| LogReg | 0.712 to 0.710 (flat) | 0.780 to 0.814 (+0.034) |
| Tree | 0.654 to 0.733 (+0.079) | 0.751 to 0.805 (+0.054) |
| RF | 0.695 to 0.713 (+0.018) | 0.782 to 0.813 (+0.031) |

The biggest change was with the Decision Tree. Adding diabetes increased its F1 score from 0.654 to 0.733 and its ROC-AUC from 0.751 to 0.805. Logistic Regression had almost no change in F1, but its ROC-AUC increased from 0.780 to 0.814. This suggests that the tree models were better able to use the diabetes feature to make predictions.

### Final Model: Tree Model 2

I selected **Tree Model 2** as the final model. It uses a Decision Tree Classifier trained on FFRPTH20, MEDHHINC21, POVRATE21, and CDC_DIABETES, with max_depth = 5 and min_samples_leaf = 5.

Reasons for choosing it:

1. **Best F1 score (0.733).** F1 was my main metric during tuning, and Tree Model 2 had the highest F1 score.
2. **Best accuracy (0.725).** It had the highest accuracy of all the models.
3. **Best recall (0.766).** It identified the most counties that actually had high obesity.
4. **Strong ROC-AUC (0.805).** It was not the highest ROC-AUC, but the difference from the highest score of 0.814 was small.

### Tradeoff: Tree Model 2 vs. LogReg Model 2

LogReg Model 2 had the highest ROC-AUC at 0.814. This means it was slightly better at separating the two classes across different thresholds. If the goal were to rank counties based on their predicted risk, LogReg Model 2 could be a good choice.

However, I used F1 as my main metric because I am more interested in making accurate classifications. Tree Model 2 had a higher F1 score, accuracy, and recall. It also identified more of the counties that actually had high obesity.

### Tradeoff: Tree Model 2 vs. RF Model 2

Random Forest did not perform better than the single Decision Tree in this project. RF Model 2 had an F1 score of 0.713 and an accuracy of 0.709, while Tree Model 2 had an F1 score of 0.733 and an accuracy of 0.725.

One possible reason is the size of the dataset. There are fewer than 2,000 counties in the training data, so a single tuned tree can still perform well. Another possible reason is the hyperparameter settings. GridSearchCV selected max_depth = 5 and min_samples_leaf = 5 for Tree Model 2, while the Random Forest grid did not include min_samples_leaf.

This shows that a more complex model is not always better. In this case, the single Decision Tree performed better than the Random Forest on the main metrics I used.

### Summary of Selection Decision

| Criterion | Winner |
|---|---|
| Accuracy | Tree Model 2 (0.725) |
| F1 | Tree Model 2 (0.733) |
| Recall | Tree Model 2 (0.766) |
| ROC-AUC | LogReg Model 2 (0.814) |
| Interpretability | Tree Model 2 (single tree is easy to visualize) |

Tree Model 2 performed best on four of the five criteria, while LogReg Model 2 had the highest ROC-AUC. Based on the F1 score, accuracy, recall, and interpretability, I selected Tree Model 2 as the final model.

## 8. Model Interpretation and Insights

### Feature Importances

The final model is Tree Model 2, a decision tree trained on four features. A decision tree reports how much each feature helped it split the counties into high and low obesity. A higher number means the tree relied on that feature more.

| Feature | Importance |
|---|---|
| CDC_DIABETES | 0.814 |
| FFRPTH20 | 0.082 |
| MEDHHINC21 | 0.079 |
| POVRATE21 | 0.024 |

Diabetes prevalence makes up 81.4% of the tree's decisions. The other three features add up to less than 19%. This matches what I saw in Section 4, where diabetes had the strongest correlation with obesity (r = 0.672). The features I focused on in Project 1, poverty, income, and fast food density, barely matter once diabetes is included.

### Why This Matters

I think there are two ways to look at this.

**Diabetes really is a strong predictor of obesity.** The two conditions are closely linked, and the CDC PLACES estimates for both come from the same survey. So it makes sense that a model would lean on one to predict the other.

**The model may not be telling us much about obesity itself.** If most of the decisions come from diabetes, then the model is basically saying that counties with high diabetes also have high obesity. That is true, but it doesn't show that poverty or the food environment can predict obesity. Those features get mostly ignored when diabetes is available.

Both points are fair. Model 1 was meant to test this. Without diabetes, the tree's F1 was 0.654, compared to 0.733 with it. So diabetes does improve the predictions, but it also raises a question about how useful the model is, which I come back to in Section 9.

### Confusion Matrix

This is how Tree Model 2 did on the 494 test counties.

|  | Predicted Low Obesity | Predicted High Obesity |
|---|---|---|
| **Actual Low Obesity** | 171 | 79 |
| **Actual High Obesity** | 57 | 187 |

Breaking this down:

- **True Negatives (171):** Counties correctly predicted as low-obesity
- **False Positives (79):** Counties wrongly flagged as high-obesity
- **False Negatives (57):** High-obesity counties the model missed
- **True Positives (187):** Counties correctly predicted as high-obesity

### Classification Report

| Class | Precision | Recall | F1 | Support |
|---|---|---|---|---|
| Low Obesity (0) | 0.750 | 0.684 | 0.715 | 250 |
| High Obesity (1) | 0.703 | 0.766 | 0.733 | 244 |
| Overall accuracy |  |  | 0.725 | 494 |

The model found 76.6% of the high-obesity counties, but it still missed 57 of them and wrongly flagged 79 low-obesity counties. Precision is a little higher for the low-obesity class (0.750 vs. 0.703), but the gap is small. The model is about equally good at both classes.

### Where the Model Performs Well

The model does about equally well on both classes. Its F1 is 0.715 for low-obesity counties and 0.733 for high-obesity counties, and it is best at catching high-obesity counties, with a recall of 0.766. I didn't run a separate error analysis, so I can't say exactly which kinds of counties it gets right most easily. Since the tree relies so heavily on diabetes, I would expect it to do best where diabetes clearly lines up with obesity.

### Where the Model Performs Poorly

The model makes 136 errors out of 494 counties (79 false positives and 57 false negatives), which is about 27.5%. I would expect many of these to be counties where diabetes doesn't line up with obesity, and where income, poverty, and fast food density don't make up the difference. I didn't check this, so it would be a good next step.

County-level obesity also depends on things this dataset doesn't include, like age, physical activity, food culture, and access to health care. When the features I have don't point clearly one way, the model is mostly guessing.

### What the Model Learned

The main thing the model learned is that counties with higher diabetes prevalence tend to have high obesity. After that, it uses fast food density and median income to sort some of the remaining counties, but those features have a small effect.

This doesn't mean food environment and income are unimportant for obesity. It means they add very little once diabetes is already in the model.

### What We Cannot Conclude

Several things cannot be concluded from this model:

1. **Causality.** The model shows that diabetes can predict obesity at the county level. It does not show that diabetes causes obesity, or the reverse.
2. **Individual behavior.** A county with high predicted risk doesn't tell me anything about a specific person who lives there. This is the same ecological fallacy issue from Project 1.
3. **Stability over time.** The model was trained on 2020 to 2023 data, so it would need to be retrained for later years.
4. **That socioeconomic factors do not matter.** Model 1 still reached an F1 of 0.654 without diabetes, so those features do carry some information. They just get overshadowed once diabetes is added.
5. **That the model should replace expert judgment.** It could work as a screening tool, but public health decisions depend on local details the model can't see.

### Main Takeaway

The final model does reasonably well, with an F1 of 0.733 and an accuracy of 0.725. But most of that comes from one feature, diabetes prevalence. That makes me wonder whether this model is useful for public health planning, or whether it just labels counties that were already known to have health problems. I look at this more in Section 9.

## 9. Limitations, Ethics, and Reflection

### Dataset Limitations

Every dataset has gaps. These are the ones that matter most for how this model should be used.

**Modeled estimates, not measurements.** The CDC PLACES obesity and diabetes values are estimates built from BRFSS survey data and modeling. They are not direct measurements of every resident. PLACES also reports confidence intervals for these estimates, but I did not use them in this analysis. That means the real obesity rate in a county could be different from the number the model sees.

**Different years across sources.** The USDA food environment data is from 2020, the income and poverty data is from 2021, and the CDC health data is from 2023. There is a gap of 2 to 3 years between the oldest and newest variables. Things like fast food density change slowly, so this probably isn't a big problem, but I can't say it has no effect.

**Lost about 21% of counties.** The final dataset has 2,468 of the roughly 3,144 U.S. counties. Counties with missing or suppressed data were dropped. These drops probably aren't random. Small, rural counties are more likely to have missing values, so the model is likely trained mostly on larger, more urban counties. Its predictions for smaller counties may be less reliable.

**Only four features.** The model has four predictors, and Model 1 only has three. That is not many for something as complicated as county-level obesity. Things like age, physical activity, food culture, and local health care access are not in the model.

**County-level data.** Each row is a county, not a person. The model can't say anything about individual behavior. This is the ecological fallacy, and I think it is the biggest limit on how the results can be interpreted.

### Sources of Bias

Several biases could affect the results:

1. **Reporting bias.** Counties with more complete data are more likely to be in the final dataset. These counties are probably larger and more urban, so the model may reflect patterns from those places more than from others.
2. **Survey bias.** BRFSS is a phone survey and the answers are self-reported. People who answer surveys may not represent everyone in a county.
3. **Feature selection bias.** I chose which features to include. Someone else might have picked different ones and gotten different results.
4. **Median cutoff bias.** I used 38.0% as the cutoff, which is the median of the counties left after cleaning. That was my decision. A different cutoff, like 40% or the 75th percentile, would change the labels and might change which model performs best.
5. **Historical bias in the data.** Obesity and diabetes rates are shaped by long-term policy, economics, and inequality. The model picks up the results of those things but not the causes.

### Consequences of Prediction Errors

The model makes two kinds of mistakes, and they have different consequences.

**False positives (79 counties).** A low-obesity county gets flagged as high-obesity. If a health department used the model to decide where to send resources, this county might get money it doesn't need. The cost is wasted resources, and the county could also be mislabeled.

**False negatives (57 counties).** A high-obesity county gets missed. I think this is the more serious error. A county with real health needs wouldn't be flagged, so it might not get the support it needs.

Which error matters more depends on the goal. If the goal is to reach every high-risk county, recall matters most, and missing 57 counties is a lot. If the goal is to use limited funding carefully, precision matters more, and 79 false alarms is a problem. The model balances the two with an F1 of 0.733, but it doesn't fully meet either goal.

### The Diabetes Problem

I think the diabetes feature is the biggest issue in this project.

CDC_DIABETES makes up 81.4% of the final tree's feature importance. Without it, the best tree (Tree Model 1) had an F1 of 0.654, compared to 0.733 with it. So diabetes is doing most of the work.

This creates two problems:

1. **Clinical overlap.** Obesity and diabetes are closely linked, so using one to predict the other is basically predicting a health outcome from a related health outcome. The poverty, income, and fast food features from Project 1 matter much less once diabetes is included.

2. **Real-world usefulness.** If a health department already knows which counties have high diabetes, it may not need a model to tell it which counties have high obesity. The model might mostly flag counties that are already known to have health problems. I didn't test this, so I can't say for sure.

There is one possible counterargument. Diabetes data might be available before updated obesity estimates are. If so, diabetes could work as an early signal. But that is a narrow case, and it doesn't change the main problem.

### Should This Model Be Used in the Real World?

Probably not as a standalone decision tool. These are my reasons:

- **Too few features.** Four predictors is not enough for something this complicated.
- **Diabetes dominance.** The model mostly relabels counties based on diabetes instead of finding new patterns.
- **Ecological fallacy.** County-level predictions can't be used to make decisions about individuals.
- **Dropped counties.** 21% of counties are missing, and they probably aren't missing at random.
- **Static model.** The data covers 2020 to 2023, so the model would need to be retrained for later years.

The model could still be useful as one input among many. An analyst could use it to flag counties for a closer look, but not to make final funding decisions. It could also be a starting point for a better model with more features and more local context.

### What I Would Do Next

If I kept working on this project, I would do three things:

1. **Drop diabetes and rebuild.** Without diabetes, the model would have to rely on income, poverty, and food environment features alone. That would show whether those features can predict obesity by themselves, or whether diabetes was covering up their signal.
2. **Add more features.** The USDA Food Environment Atlas and CDC PLACES both have more variables, such as SNAP participation, grocery store access, and physical inactivity. I would check which ones are available at the county level and add them.
3. **Test different cutoffs.** Instead of the median, I would try the 75th percentile or a clinical cutoff like 40%. This would show how much the results depend on how I defined the classes.

I would also try a regression model that predicts the actual obesity rate instead of a high or low label. That would remove the cutoff problem completely.

### What Users Should Understand

Anyone using this model should keep three things in mind:

1. The model predicts county-level obesity mostly from diabetes prevalence. It is not finding new risk factors.
2. The predictions are about counties, not people.
3. The model was trained on a specific window of data and should not be assumed to hold for other years without re-training.

If these limits are understood, the model can be a useful starting point. If they are ignored, it could lead to resources going to the wrong places and too much trust in the predictions.

## 10. Code and Transparency

### Notebook and Code

The full notebook for this project is on my GitHub portfolio. It has all of the data cleaning, exploration, modeling, and evaluation steps, and it runs top to bottom.

**Notebook link:** [add your GitHub link here]

The notebook includes:
- USDA Excel data loading and sheet selection
- CDC PLACES API pull via the Socrata endpoint
- Merge on FIPS code
- Missing value handling
- Feature construction and target binarization
- Exploratory visualizations
- Model training, tuning, and evaluation

### Dataset Citations

**USDA Food Environment Atlas**
U.S. Department of Agriculture Economic Research Service. (2025). *Food Environment Atlas*. https://www.ers.usda.gov/data-products/food-environment-atlas/

**CDC PLACES**
Centers for Disease Control and Prevention. (2023). *PLACES: Local data for better health*. U.S. Department of Health and Human Services. https://www.cdc.gov/places

**CDC PLACES API**
Data pulled live from `https://data.cdc.gov/resource/swc5-untb.json` with filters for `measureid=OBESITY` and `measureid=DIABETES`.

### Sources Cited in the Project

Abraham, A. M., Swartz, M. D., van den Berg, A. E., & Linder, S. H. (2026). Multilevel analysis of the food and physical activity environment and adult obesity across U.S. counties and states. *International Journal of Environmental Research and Public Health*, *23*(2), Article 142. https://doi.org/10.3390/ijerph23020142

Emmerich, S. D., Fryar, C. D., Stierman, B., & Ogden, C. L. (2024). *Obesity and severe obesity prevalence in adults: United States, August 2021 to August 2023* (NCHS Data Brief No. 508). National Center for Health Statistics. https://doi.org/10.15620/cdc/159281

Ezendu, K., Pohl, G., Lee, C. J., Wang, H., Li, X., & Dunn, J. P. (2025). Prevalence of obesity-related multimorbidity and its health care costs among adults in the United States. *Journal of Managed Care & Specialty Pharmacy*, *31*(2), 179–188. https://doi.org/10.18553/jmcp.2025.31.2.179

### Tools and Libraries

The analysis used:

- **Python 3.13**
- **pandas** for data handling
- **numpy** for numerical operations
- **matplotlib** and **seaborn** for visualizations
- **requests** for the CDC Socrata API
- **scikit-learn** for modeling (Logistic Regression, Decision Tree, Random Forest, GridSearchCV, StandardScaler)

### AI Usage Disclosure

In accordance with the course policy, I am disclosing how generative AI tools were used in this project.

**Tool used:** Claude (Anthropic) via the web interface.

**Purposes:**
- Debugging Python errors during the notebook setup (for example, the missing scikit-learn install)
- Reviewing the structure of the project sections against the assignment rubric
- Providing feedback on the clarity of my write-ups

**What I did myself:**
- All data collection and cleaning decisions
- All code execution and verification
- All modeling choices (which models, which features, which hyperparameters)
- All interpretation of results
- All final writing and edits

AI was used as a supporting tool for debugging, organization, feedback, and rewording some of the write-up. It did not run the analysis or produce any of the results. The numbers and findings come from my own code and data.

### Reproducibility

To reproduce this project:

1. Request a Census API key from `https://api.census.gov/data/key_signup.html` (only needed if pulling census data; CDC PLACES API does not require a key)
2. Download the USDA Food Environment Atlas from `https://www.ers.usda.gov/data-products/food-environment-atlas/`
3. Run the notebook top to bottom with `random_state=42` fixed everywhere
4. Results should match the tables in Sections 6, 7, and 8

### Final Note

The project represents my own work and analysis. Any errors or gaps are my own. The AI disclosure above reflects the actual use of tools during this project.
