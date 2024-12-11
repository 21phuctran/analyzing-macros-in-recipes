# Analyzing the relationship between Macros Percentage and Rating of Recipes

Author: Phuc Tran

## Introduction

Bodybuilding involves progressive resistance exercise to build and shape muscles through hypertrophy. While hard work in the gym is critical, nutrition plays an equally important role. A commonly recommended guideline for muscle building is to consume 0.7–1g of protein per pound of body weight, which can be challenging to achieve, especially during a calorie deficit (cutting). Recipes high in protein but balanced in other macros are crucial in such cases.

With the rising popularity of health and fitness, platforms like Food.com play a key role in helping users discover recipes that meet their dietary needs. In this analysis, I aim to explore whether recipes with a higher protein macro percentage tend to receive higher ratings on Food.com. This question is important for understanding how nutritional composition, particularly protein content, influences user preferences.

The dataset interactions contains 731927 user reviews on each food with 5 columns below

| Column        | Description         |
| :------------ | :------------------ |
| `'user_id'`   | User ID             |
| `'recipe_id'` | Recipe ID           |
| `'date'`      | Date of interaction |
| `'rating'`    | Rating given        |
| `'review'`    | Review text         |

The dataset recipe contains 83782 rows and 12 columns. This means that there are 83782 unique recipes with 12 columns below

| Column             | Description                                                                                                                                                                                       |
| :----------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `'name'`           | Recipe name                                                                                                                                                                                       |
| `'id'`             | Recipe ID                                                                                                                                                                                         |
| `'minutes'`        | Minutes to prepare recipe                                                                                                                                                                         |
| `'contributor_id'` | User ID who submitted this recipe                                                                                                                                                                 |
| `'submitted'`      | Date recipe was submitted                                                                                                                                                                         |
| `'tags'`           | Food.com tags for recipe                                                                                                                                                                          |
| `'nutrition'`      | Nutrition information in the form [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]; PDV stands for “percentage of daily value” |
| `'n_steps'`        | Number of steps in recipe                                                                                                                                                                         |
| `'steps'`          | Text for recipe steps, in order                                                                                                                                                                   |
| `'description'`    | User-provided description                                                                                                                                                                         |
| `'ingredients'`    | Text for recipe ingredients                                                                                                                                                                       |
| `'n_ingredients'`  | Number of ingredients in recipe   

To investigate this, I will analyze the relationship between protein content and user ratings using statistical methods and data visualization techniques. By uncovering this relationship, recipe developers can create more appealing high-protein recipes, and individuals pursuing fitness goals can make informed choices without compromising on satisfaction or popularity.

## Data Cleaning and Exploratory Data Analysis

To prepare the dataset for analysis, we performed several data cleaning steps to ensure its usability and accuracy. Below is a detailed breakdown of the steps:

### 1. Merging Datasets
The `recipes` and `interactions` datasets were merged using a left join on the `id` column from `recipes` and the `recipe_id` column from `interactions`. This allowed us to link recipes with their corresponding ratings and reviews. The resulting dataset contained 234,429 rows and 17 columns.

### 2. Handling Missing Values
We identified and addressed missing values in the merged dataset:
- One recipe had a missing name, but the rest of its data was valid, so it was retained.
- A single row had missing values for key columns (`rating`, `user_id`, and `recipe_id`). This row was removed as it provided no meaningful information.
- Ratings of `0` were replaced with `NaN` as they were likely placeholders for missing data, not actual ratings.

### 3. Extracting Nutritional Information
The `nutrition` column in the `recipes` dataset contained lists of nutritional values in percentage daily values (PDVs). These were split into individual columns for each nutritional component: `calories (#)`, `total fat (PDV)`, `sugar (PDV)`, `sodium (PDV)`, `protein (PDV)`, `saturated fat (PDV)`, and `carbohydrates (PDV)`.

### 4. Converting Nutritional Percentages to Grams
To make the nutritional data more interpretable, we converted the PDVs into grams based on recommended daily values (RDV). For instance:
- `protein_grams` was calculated as `(protein (PDV) / 100) * RDV_protein`.
- Similar calculations were performed for fat and carbohydrates.

Additionally, the total macro composition (`protein_grams`, `fat_grams`, and `carbs_grams`) was summed, and the percentage of each macro was calculated.

### 5. Tagging High-Protein Recipes
The `tags` column in the `recipes` dataset contained descriptors like `high-protein` and `dessert`. We extracted this information by checking for the presence of the `high-protein` tag and created a new binary column, `high protein`, to flag such recipes.

### 6. Handling Special Cases
Some recipes, such as sauces and spice mixes, had zero values for all macros. These rows were flagged, and their macro proportions were left as `NaN` to avoid division errors.

### 7. Finalizing the Cleaned Dataset
After data cleaning, the final dataset consisted of 32 columns, including the derived features such as macro percentages, grams, and binary flags for high-protein recipes. 

After performing the necessary data cleaning steps, the dataset was refined to include the most relevant columns for analysis. The final dataset consists of 26 columns and retains key features such as recipe names, preparation times, submission dates, tags, steps, and nutritional information. Below is a preview of the cleaned dataset:

| **Name**                              | **ID**  | **Minutes** | **Submitted** | **Tags**                                  | **n_steps** | **Steps**                          | **n_ingredients** | **Date**       | **Rating** | ... | **Carbohydrates (PDV)** | **Protein (grams)** | **Fat (grams)** | **Carbs (grams)** | **Total Macros** | **Protein (%)** | **Fat (%)** | **Carbs (%)** | **High Protein** | **Sugar Proportion** |
|---------------------------------------|---------|-------------|---------------|-------------------------------------------|-------------|------------------------------------|--------------------|----------------|------------|-----|--------------------------|---------------------|-----------------|------------------|------------------|------------------|--------------|----------------|-----------------|---------------------|
| 1 brownies in the world best ever     | 333281  | 40          | 2008-10-27    | ['60-minutes-or-less', 'time-to-make',...] | 10          | ['Heat the oven to 350°F...',...] | 9                  | 2008-11-19     | 4.0        | ... | 6.0                      | 1.5                 | 7.8             | 16.5             | 25.8             | 5.81            | 30.23        | 63.95          | False            | 0.361               |
| 1 in canada chocolate chip cookies    | 453467  | 45          | 2011-04-11    | ['60-minutes-or-less', 'time-to-make',...] | 12          | ['Preheat the oven to 350°F...',...] | 11                 | 2012-01-26     | 5.0        | ... | 26.0                     | 6.5                 | 35.88           | 71.5             | 113.88           | 5.71            | 31.51        | 62.79          | False            | 0.355               |
| 412 broccoli casserole                | 306168  | 40          | 2008-05-30    | ['60-minutes-or-less', 'time-to-make',...] | 6           | ['Preheat oven to 350°F...',...] | 9                  | 2008-12-31     | 5.0        | ... | 3.0                      | 11.0                | 15.6            | 8.25             | 34.85            | 31.56           | 44.76        | 23.67          | False            | 0.031               |
| 412 broccoli casserole                | 306168  | 40          | 2008-05-30    | ['60-minutes-or-less', 'time-to-make',...] | 6           | ['Preheat oven to 350°F...',...] | 9                  | 2009-04-13     | 5.0        | ... | 3.0                      | 11.0                | 15.6            | 8.25             | 34.85            | 31.56           | 44.76        | 23.67          | False            | 0.031               |
| 412 broccoli casserole                | 306168  | 40          | 2008-05-30    | ['60-minutes-or-less', 'time-to-make',...] | 6           | ['Preheat oven to 350°F...',...] | 9                  | 2013-08-02     | 5.0        | ... | 3.0                      | 11.0                | 15.6            | 8.25             | 34.85            | 31.56           | 44.76        | 23.67          | False            | 0.031               |

### Univariate Analysis
We first examined the distribution protein macro percentage in the recipe. As the plot belows reveals, the distribution is skewed to the right, which suggests that most recipes on food do not have high protein percentage in their nutrition. Additionally, as the protein percentage increases, there are less recipes. 

<iframe
  src="assets/protein_percentage_distribution.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Bivariate Analysis
The box plot illustrates the distribution of macro percentages (protein, fat, and carbohydrates) across different user ratings for recipes. Higher-rated recipes (ratings 4 and 5) tend to have more balanced macro distributions, with fat and carbohydrates often dominating over protein, reflecting user preferences for these compositions.

<iframe
  src="assets/macro_comparison_by_rating.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Interesting Aggregates

## Assessment of Missingness

### NMAR Analysis

In the context of recipe reviews, missing reviews might be NMAR because users tend to leave a review only if they feel strongly about the recipe (positive or negative) or if the recipe significantly meets or fails their expectations. If users try a recipe and find it unremarkable or not worth mentioning, they might skip leaving a review altogether, effectively "moving on." This behavior means the likelihood of a missing review depends on the user's perception of the recipe's quality or relevance, which is not explicitly captured in the dataset.

### Missingness Dependency

## Hypothesis Testing
Null Hypothesis: The average rating of high-protein recipes is equal to the average rating of non-high-protein recipes. Alternative Hypothesis: The average rating of high-protein recipes is different from the average rating of non-high-protein recipes. Test Statistic: The difference in mean ratings between the high-protein and non-high-protein recipe groups. Significance Level: 0.05.

We conducted a permutation test to determine whether high-protein recipes receive significantly different ratings compared to non-high-protein recipes. A permutation test is appropriate because it does not assume any specific population distribution, making it ideal for situations where prior knowledge about the population is unavailable.

The test statistic, the difference in mean ratings, was chosen because it directly measures the relationship between recipe ratings and high-protein content. This aligns with our directional hypothesis that high-protein recipes might receive higher ratings due to perceived health benefits.

The observed difference in mean ratings was -0.0146, suggesting a slight decrease in ratings for high-protein recipes compared to non-high-protein ones. To evaluate the significance of this difference, we generated a null distribution by shuffling group labels (high-protein vs. non-high-protein) 1,000 times and calculating the test statistic for each shuffle. The resulting p-value of 1.000 indicates that the observed difference is entirely consistent with the null hypothesis.

<iframe
  src="assets/macro_comparison_by_rating.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Conclusion: Based on this analysis, we fail to reject the null hypothesis. This suggests that high-protein content does not significantly influence recipe ratings. These results imply that other factors, such as taste, preparation time, or ingredient variety, may play a larger role in determining user ratings on this platform.

## Framing a Prediction Problem

Our prediction problem aims to predict the rating of a recipe based on its features, making this a regression task.

The response variable is rating, a continuous variable representing user evaluations.

Predictors include recipe preparation attributes such as minutes (cooking time), n_steps (number of steps), and n_ingredients (number of ingredients), as well as nutritional information like protein_percentage, carbs_percentage, and fat_percentage.

We will evaluate the model using Mean Absolute Error (MAE), chosen for its interpretability in measuring the average magnitude of prediction errors, and R-squared to assess the overall variance explained by the model. These metrics are well-suited for regression and allow us to measure the accuracy and effectiveness of the predictions. The selected features are available at the time of prediction, making them valid inputs for this task.

## Baseline
Our prediction problem aims to predict the rating of a recipe based on its features, making this a regression task.

The response variable is rating, a continuous variable representing user evaluations. Predictors include recipe preparation attributes such as n_steps (number of steps) and high protein (a Boolean variable indicating whether the recipe is tagged as high protein). These features are chosen because they are readily available before a recipe is rated, making them valid predictors.

We will evaluate the model using Mean Absolute Error (MAE), chosen for its interpretability in measuring the average magnitude of prediction errors, and R-squared (R²) to assess the overall variance explained by the model. These metrics are well-suited for regression and allow us to gauge the accuracy and effectiveness of the predictions.

The baseline model, a simple linear regression using the selected features, achieved the following results:

MAE: 0.4962
R²: 0.00004

While the low R² value suggests that the model explains very little of the variance in ratings, the MAE indicates the average prediction error is around 0.4962 rating points. This result highlights that the current model has room for improvement, particularly in capturing the complexity of user rating patterns. Future steps will focus on incorporating additional features and more advanced modeling techniques to enhance performance.
## Final
For the final model, we improved upon the baseline model by engineering new features and performing hyperparameter tuning. Below are the details of the features we added, the transformations applied, and the rationale behind these choices.

### Features and Transformations

#### 1. `'is_dessert'`
This column categorizes recipes as either dessert or non-dessert, determined by checking if the recipe's tags contain the keyword `'dessert'`. From exploratory data analysis (EDA), we observed that higher ratings (4 and 5) are less frequent among dessert recipes. This trend suggests that `'is_dessert'` could be a useful predictor of ratings. We one-hot encoded this categorical feature, as it effectively represents the distinction between desserts and non-desserts in a way the model can understand.

#### 2. `'minutes'`
This column represents the cooking time of the recipe in minutes. From our bivariate analysis of `'minutes'` and `'rating'`, we found that recipes with longer cooking times tend to receive mediocre ratings (e.g., ratings of 2 or 3). This observation aligns with the intuition that recipes requiring more time to prepare might be less appealing to busy individuals. To make the values comparable across all recipes, we standardized the `'minutes'` feature using `StandardScaler`, ensuring that extreme values do not disproportionately influence the model.

#### 3. `'calories (#)'`
This column indicates the total calorie count of a recipe. EDA showed that recipes with higher ratings generally have fewer calories. Lower calorie counts are often associated with healthier recipes, which may be more appealing to users. Given that the `'calories (#)'` feature contains many outliers, we transformed it using `RobustScaler`. This scaler minimizes the effect of outliers and ensures that the model is less biased by extreme values.

#### 4. `'n_steps'`
This column represents the number of steps required to prepare a recipe. Bivariate analysis revealed that recipes with fewer steps are more likely to receive higher ratings. This trend likely reflects user preference for simpler and less time-intensive recipes. We used `StandardScaler` to normalize this feature and ensure it is on a comparable scale with other features.

### Model Selection and Hyperparameter Tuning
For our final model, we used a `LinearRegression` algorithm. We incorporated polynomial features to capture potential interactions and nonlinear relationships between predictors. We used `GridSearchCV` to perform hyperparameter tuning, specifically testing degrees of polynomial features (1, 2, and 3). This process ensured we identified the degree of polynomial features that minimized the mean absolute error (MAE).

### Results
The best model used a polynomial degree of 1, resulting in the following performance metrics:
- **Mean Absolute Error (MAE):** 0.4962
- **R-squared (R²):** 0.000045

These results demonstrate a slight improvement over the baseline model. The additional features, particularly those engineered from our EDA findings, contributed to the model's ability to explain some variance in user ratings. However, the relatively low R² indicates that other factors not captured in our dataset may play a significant role in determining user ratings.
