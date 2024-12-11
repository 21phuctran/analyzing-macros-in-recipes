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

Before we analyze the data, we must clean it

1. We first merge the two datasets together to get their rating and review

2. 
#### Result

### Univariate Analysis

### Bivariate Analysis

### Interesting Aggregates

## Assessment of Missingness

### NMAR Analysis

### Missingness Dependency

## Hypothesis Testing

#### Conclusion of Permutation Test

## Framing a Prediction Problem