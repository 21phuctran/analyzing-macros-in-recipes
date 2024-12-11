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

### Bivariate Analysis

### Interesting Aggregates

## Assessment of Missingness

### NMAR Analysis

### Missingness Dependency

## Hypothesis Testing

#### Conclusion of Permutation Test

## Framing a Prediction Problem