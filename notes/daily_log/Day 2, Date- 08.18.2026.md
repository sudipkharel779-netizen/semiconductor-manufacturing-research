Date: July 27, 2026 | Day: 2

## What I completed

- [x] Downloaded SECOM dataset
- [x] Built project folder structure
- [x] Written README
- [x] EDA notebook — missing data, distributions, pass/fail comparison
- [x] 3 figures saved to reports/figures/
- [x] GitHub committed and pushed
- [x] Paper annotated

## Key finding from the SECOM dataset

538 out of 590 columns have missing data, 116 columns have zero
variance, and only 104 out of 1567 wafers failed — confirming this
is a severely imbalanced, high-dimensional dataset requiring careful
preprocessing before any modeling.

## The class imbalance problem

A model that predicts pass for every wafer gets 93.36% accuracy but
catches zero failures. Accuracy hides this because failures are only
6.64% of the data. PR-AUC and recall are better metrics because they
specifically measure how many real failures we caught.

## Paper annotated

Predictive Maintenance of Semiconductor Equipment Using Stacking
Classifiers and Explainable AI (December 2025)

1. This paper proposes fault prediction by integrating stacking
   classifiers, synthetic data generation, and XAI techniques.
2. The stacking classifier with synthetic data achieved higher fault
   detection accuracy than single models, with SHAP identifying the most
   critical equipment parameters.
3. The model was tested on synthetic data only, raising questions about
   generalizability to real-world semiconductor sensor data.
4. My SECOM project extends this by applying ensemble methods and SHAP
   to real sensor data with severe class imbalance — addressing the
   generalizability gap this paper leaves open.

## Open questions

1. What do we actually do with the 28 columns that are 50-90% missing
   — drop or impute, and how do we decide?
2. How do we choose the right imputation method for columns with low
   missingness — mean, median, or model-based?
3. Can SHAP produce reliable feature importance when features have
   heavy missingness and near-zero variance?

## Connection to research identity

Today's work demonstrates I can handle real-world messy semiconductor
data — missing values, class imbalance, high dimensionality — which
directly supports professor outreach in October showing research
readiness, not just coursework.

## What I learned

1. The dataset contains 1,567 observations and 591 features. Each row is one production lot; each column is one sensor measurement or process parameter recorded during manufacturing
2. **What does a 6.64% failure rate mean for a semiconductor fab? Is that good or bad from a manufacturing perspective?**
   -->6.64% failure rate mean 104 wafer were identified as failure during inspection. From Manufacturing perspective, it is a bad number specially when we are talking about semiconductor industry since those 6.64% failure is total waste for company after processing them through many steps that constitute time ,labor, resources etc. for company.
3. **If you trained a model that predicted "pass" for every single wafer, what accuracy would it get?**
   -->If we trained a model that predicted "pass" for every single wafer,  we would get 93.36% accuracy.
4. **Why would that be a completely useless model despite the high accuracy?**
   -->If we have 100% accuracy, that would be useless model because it is not being able to catch the real failure. our main goal is to catch the real failure before it goes to next step. when model misses real failure then it cost company downside only.
5. **What metric should you use instead of accuracy, and why?**
   -->Instead of accuracy, we should use PR AUC number because it takes both precision and especially recall which is the most important one because we want to have high recall meaning out of total failure our model wants to catch as much as we can.
6. **1. 538 out of 590 columns have missing data. What does that tell you about this dataset — is missingness an edge case or a fundamental characteristic you have to design around?**
   --> its a fundamental characteritists we have to build around, coz among those 538 majority of them are under 50% . so we should try to find number of column with missing value under each small percentage range, coz column mssing 0.01% value and one missing 49% value are not same and have totally different impact and we cant consider 0-50% range here.then only we can figure out more deeply.
7. **The overall missing rate is only 4.54%, but 28 columns have more than 50% missing. How is that possible? What does that tell you about _where_ the missingness is concentrated?**
   -->so with 28 columns missing more than 50% and 4 missing more than 90% vakue, it provide us important information that most of of the missing value among total are from these columns. so we should insect these sensor first and figure out the reason behind it and how much impact it has in yield prediction, how can we solve this problem whether replacing them or totalling removing them etc.
8. **What would you do with those 4 columns that are more than 90% missing? Why?**
   -->with these 4 coumns missing 90% value, we dont have enough data to learn about them . so most likley this sensor is not among imprtant signal and could be noise. so we are gonna remove these coulmns.
9. **Can you just replace all missing values with the column mean and move on? Why or why not?**
   -->well it depends, we have to calculate their variance. if variance is large then we cant replace with its mean. but if its small then we can replace with mean . But when we are replacing missing values number of missing values should not be much higher other wise it would be highly biased and effect in yield prediction. lets say one sensor have 10% missing value and rest of them are around very small range except some outlier which could be from different reason then we can use mean, but if same sensor has 70% missing value then it would be very high biased to replace them with remaining value mean.
10. - **Why printing alone isn't enough?What the visualization will show?Why it matters for your strategy?**
      --> printing number alone is not enough because number does not provide the trend , distribution like chart does. engineers use chart to shows the data and present it to team. using chart can give us hint of upcoming probability. easy to analyze the data in chart than numbers alone. chart will show us missing value in small range and help us to plan accordingly what we gonna do with those missing value which have big impact in final output.
11. 90%+ missing→ drop
    60% missing→ investigate further
    Under 10% missing→ analyze distribution then decide imputation method
12. **print(X.iloc[:, :10].describe().round(3))** : mean all rows and :10 mean first 10 column so combine of all rows of first 10 columns. This function is important to describe basic statistical info.
13. **You have 116 zero variance columns out of 590. What does that mean for your feature space before you even start modeling?**
    --> 116 zero variance column carry no any important info to help us in yield prediction so its no use of more next stage and we gonna drop them to identify other important signal.
14. **You have 221 near-zero variance columns. Combined with the 116 zero variance — that's over half your features carrying almost no information. What does that tell you about the real dimensionality of this dataset?**
    --> this tells us that over hald of the senor carry no imortant info for yield prediction and they were constant throuout the process and hasnot impact any thing to contribute the defect of wafer. so wafer failure failur is depended upon very few sensor.
15. **Look at column 3 — mean is 1396 but max is 3715 and min is 0. What does that suggest about that sensor's data?**
    --> suggest vary high variance and give us alert right away to analyze this sensor with cautious and also since its not around one range , all over the place something wrong went in process. also it has 14 missing value this might also effect for high variance.min is 0.0 which for a physical sensor measurement like temperature or pressure is suspicious. A reading of zero could mean the sensor failed or wasn't recording, not that the process value was actually zero. That's worth flagging.
16. - Features have vastly different scales and distributions, Standardization required before modeling, Features 200, 300, 500 show heavy skew — note for preprocessing.
17. - Most features show heavy overlap between pass and fail distributions — confirms this is a hard classification problem requiring multivariate methods, not single-feature rules Feature 5 confirmed as zero variance — identical distribution for pass and fail because all values are constant

- No single feature shows clean separation — yield prediction requires modeling feature interactions

1.

## Questions I have

## How this connects to my research identity

## What I will do tomorrow
