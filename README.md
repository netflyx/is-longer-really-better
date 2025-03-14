# Is Longer Really Better?
## Analysis Of The Relationship Between Cooking Time And Average Enjoyment Rating.
Authors: Alyxandrea Wikarsa & Jennifer Kuei

## Overview

This project is conducted at UCSD for the DSC80 course. In this project, we are taking a look at the relationship between cooking time and average enjoyment rating based on the given dataset.

## Introduction

Who doesn't prefer good food? With so many recipes available especially online, people rely on ratings to determine which dishes are worth the time and effort. However, a myriad of people prioritize convenience as well. Especially for college students where in a study conducted by Yugo News, 63% of students that don't cook state that time is the biggest barrier. This is why we are looking to find out **if there is a relationship between the average time (minutes) of cooking and the average rating of recipes** as we want recipes that taste good and are fast.

The `recipe` dataset, taken from `RAW_recipes.csv`, contains 83782 rows, indicating 83782 unique recipes, with 10 columns recording the following information:

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
| `'n_ingredients'`  | Number of ingredients in recipe                                                                                                                                                                   |

The `interactions` dataset, taken from `interactions.csv`, contains 83782 rows, indicating 83782 unique recipes, with 10 columns recording the following information:

| Column        | Data type           |
| :------------ | :------------------ |
| `'user_id'`   | User ID             |
| `'recipe_id'` | Recipe ID           |
| `'date'`      | Date of interaction |
| `'rating'`    | Rating given        |
| `'review'`    | Review text         |

**The columns that we are mainly focused on are ‘id’ and ‘minutes’ from `recipes` and ‘recipe_id’ and ‘rating’ from `interactions`.**

## Data Cleaning and Exploratory Data Analysis

To make our dataset more convenient for our analysis, we did the following steps to clean it up:

1. Cleaning up the interactions dataset:
   - Filled in missing reviews with empty strings.
   - Convert the date to a datetime format in case we need to take data overtime.
   - Changed the values in the ‘rating’ column. The ratings are generally 1 (lowest) to 5 (highest). In order to avoid outliers, we changed all zeroes to np.nan. We also ensured that there were no ratings higher than 5.
  
  This is what `interactions_df` looks like after cleanup:
  | Column             | Data type   |
  | :----------------- | :---------- |
  | `'user_id'`        | int64       |
  | `'recipe_id'`      | int64       |
  | `'date'`           | datetime64[ns] |
  | `'rating'`         | float64     |
  | `'review'`         | object      |


2. Cleaning up the recipes dataset:
   - Filled in missing columns, such as name and description, with empty strings.
   - Convert the ‘submitted’ column values to a datetime format
   - Dropped rows that minutes less than / equal to zero. Generally, we expect cooking time to be at least one minute, so we removed zero minute values from our analysis.
   - Values that appear as lists in the dataset are actually strings. We created a function to convert it into a list object.
  
  This is what `recipes_df` looked like after cleanup:
  | Column             | Data type   |
  | :----------------- | :---------- |
  | `'name'`           | object      |
  | `'id'`             | int64       |
  | `'minutes'`        | int64       |
  | `'contributor_id'` | int64       |
  | `'submitted'`      | datetime64[ns] |
  | `'tags'`           | object      |
  | `'nutrition'`      | object      |
  | `'n_steps'`        | int64       |
  | `'steps'`          | object      |
  | `'description'`    | object      |
  | `'ingredients'`    | object      |
  | `'n_ingredients'`  | int64       |


3. Merging the datasets:
   - Our first merge was recipes (on ‘id’) and interactions (on ‘recipe_id’). We merged left, which means we kept the values on recipes. The resulting dataframe is named `merged_df`

4. Adding a column for average ratings:
   - Created a copy of the merged dataframe using groupby and added a new column, ‘avg_rating’, which shows the average rating of each recipe (grouped by ‘id’). We named this dataframe `average_ratings`.
  
  This is what `average_ratings` looks like:
  | Column             | Data type   |
  | :----------------- | :---------- |
  | `'id'`             | int64       |
  | `'avg_rating'`     | float64     |

5. Merging the datasets (again):
   - We want to create a dataframe that only has one rating for each duplicate recipe, which means we need to drop the duplicate ‘id’ values on merged_df before merging it with average_raitngs. 
Merged ‘average_ratings’ and  `deduplicated_df` on their shared column ‘id’ and kept left. The resulting dataframe is `merged_avg_df`.

### Results
Here are all the columns in `merged_avg_df`:
| Column             | Data type   |
| :----------------- | :---------- |
| `'id'`             | int64       |
| `'avg_rating'`     | float64     |
| `'name'`           | object      |
| `'id'`             | int64       |
| `'minutes'`        | int64       |
| `'contributor_id'` | int64       |
| `'submitted'`      | datetime64[ns] |
| `'tags'`           | object      |
| `'nutrition'`      | object      |
| `'n_steps'`        | int64       |
| `'steps'`          | object      |
| `'description'`    | object      |
| `'ingredients'`    | object      |
| `'n_ingredients'`  | int64       |
| `'user_id'`        | int64       |
| `'recipe_id'`      | int64       |
| `'date'`           | datetime64[ns] |
| `'rating'`         | float64     |
| `'review'`         | object      |




