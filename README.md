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

| Column    | Data Type      |
|:----------|:---------------|
| user_id   | int64          |
| recipe_id | int64          |
| date      | datetime64[ns] |
| rating    | float64        |
| review    | object         |


2. Cleaning up the recipes dataset:
   - Filled in missing columns, such as name and description, with empty strings.
   - Convert the ‘submitted’ column values to a datetime format
   - Dropped rows that minutes less than / equal to zero. Generally, we expect cooking time to be at least one minute, so we removed zero minute values from our analysis.
   - Values that appear as lists in the dataset are actually strings. We created a function to convert it into a list object.
  
This is what `recipes_df` looked like after cleanup:

| Column          | Data Type      |
|-----------------|----------------|
| id              | int64          |
| avg_rating      | float64        |
| name            | object         |
| minutes         | int64          |
| contributor_id  | int64          |
| submitted       | datetime64[ns] |
| tags            | object         |
| nutrition       | object         |
| n_steps         | int64          |
| steps           | object         |
| description     | object         |
| ingredients     | object         |
| n_ingredients   | int64          |
| calories        | object         |
| total_fat       | object         |
| sugar           | object         |
| sodium          | object         |
| protein         | object         |
| saturated_fat   | object         |
| carbohydrates   | object         |
| user_id         | float64        |
| recipe_id       | float64        |
| date            | datetime64[ns] |
| rating          | float64        |
| review          | object         |


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
Merged `average_ratings` and  `deduplicated_df` on their shared column ‘id’ and kept left. The resulting dataframe is `merged_avg_df`.

6. Filtering outliers:
   - In order to take care of outliers, we cap the minutes at the 99th percentile. **HELP: give reason why pls**
   - The resulting dataframe is named `filtered_df`, and this is the dataframe we will be working with for the rest of the project


### Results
Here are all the columns in `filtered_df`:

| Column          | Data Type      |
|-----------------|----------------|
| id              | int64          |
| avg_rating      | float64        |
| name            | object         |
| minutes         | int64          |
| contributor_id  | int64          |
| submitted       | datetime64[ns] |
| tags            | object         |
| nutrition       | object         |
| n_steps         | int64          |
| steps           | object         |
| description     | object         |
| ingredients     | object         |
| n_ingredients   | int64          |
| calories        | object         |
| total_fat       | object         |
| sugar           | object         |
| sodium          | object         |
| protein         | object         |
| saturated_fat   | object         |
| carbohydrates   | object         |
| user_id         | float64        |
| recipe_id       | float64        |
| date            | datetime64[ns] |
| rating          | float64        |
| review          | object         |



Our cleaned dataframe has 82948 rows and 25 columns. Here is a preview of the first 5 rows of our cleaned dataframe. We selected the columns that are most relevant to our questions for display. 
Scroll right to view more columns.

| id      | name                                    | minutes | avg_rating |
|:--------|:----------------------------------------|:--------|:-----------|
| 275022  | impossible macaroni and cheese pie      | 50      | 3.0        |
| 275024  | impossible rhubarb pie                  | 55      | 3.0        |
| 275026  | impossible seafood pie                  | 45      | 3.0        |
| 275030  | paula deen's caramel apple cheesecake   | 45      | 5.0        |
| 275032  | midori poached pears                    | 25      | 5.0        |


### Univariate Analysis

For our univariate analysis, we focused on examining the distribution of cooking time by minutes. The cooking time distribution shows that it is right-skewed, with most recipes taking a short time to cook and a few taking much longer.

Because we've capped the minutes at the 99th percentile, the x-axis is no longer stretched completely, so the plot is easier to read. Here, the plot seems to have a downwards sloping trend after it hits a certain point before 50 minutes.

<iframe
  src="assets/fig_univariate.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Bivariate Analysis

To explore the relationship between cooking time and average rating, we created a scatter plot and a box plot. We added the `cooking_time_bins` column to `filtered_df`. 

<iframe
  src="assets/fig_bivariate_scatter.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

<iframe
  src="assets/fig_bivariate_box.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

This is important because it allows us to categorize recipes into meaningful groups based on their cooking time — like **Short (<20 min)**, **Medium (20-60 min)**, **Long (60-120 min)**, and **Extra Long (>120 min)**. This helps reveal patterns or trends that might not be obvious when looking at raw minute values. 

By binning cooking times, we can clearly compare how different time ranges affect recipe ratings. For example, users might rate quick recipes higher due to convenience, or they might prefer longer, more complex recipes if they believe the effort results in better meals. Without these bins, the minute data would be too granular, making it harder to spot these relationships. 


### Interesting Aggregates

In our `filtered_df`, a few columns would be good to group and pivot. We added the `cooking_time_bins` column and we’ve already categorized cooking times, so grouping by these bins can reveal trends like average ratings for each time category. We can also group by the ratings to show how cooking time affects the user ratings.

| cooking_time_bins       | avg_rating (mean) | count |
|-------------------------|-------------------|-------|
| Short (<20 min)         | 4.662133          | 22894 |
| Medium (20-60 min)      | 4.609567          | 38094 |
| Long (60-120 min)       | 4.627442          | 11840 |
| Extra Long (>120 min)   | 4.588253          | 7566  |

In this pivot table:
- cooking_time_bins reflects how long a recipe takes, which can impact user satisfaction.
- avg_rating shows how users rate recipes, helping identify if quick recipes are rated better than time-consuming ones.
- mean and count lets us see both the average rating and how many recipes fall into each category.


## Assessment of Missingness
In our data, there are three columns that have a significant amount of missing values `review`, `rating`, and `avg_rating` 
### NMAR Analysis
I think that the missing values for review are not missing at random. This is due to the fact that many people might not care to leave a review unless they either loved or hated it (1 or a 5) but if they were more neutral on it, they might not have left a review.

### Missingness Dependency

To check the missingness of `avg_rating`, we tested to see if there are any columns that it depends on. So for this one, we tested if `avg_rating` was dependent on `minutes`. A hypothesis test was set up with the following hypotheses:

**Null hypothesis**: The missingness of avg ratings does not depend on the minutes column

**Alternative hypothesis**: Null hypothesis: The missingness of avg ratings does depend on the minutes column

**Test Statistic**: Absolute difference of means (avg amount of minutes with rating missing - avg amount of minutes with rating not missing)

**Significance level**: 0.01

<iframe
  src="assets/fig_marMinutes.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Interpretation: We ran the test by shuffling missingness 1000 times and we got a 130.29 observed difference which is shown on the graph. Since the p-value we got was (0) is less than our significance level (0.01), we **reject the null hypothesis** thus the missingness of `avg_ratings` is not dependent on the `minutes` it takes to complete the recipe

We did another test on missingness to check if `avg_ratings` was dependent on `protein` which involves the same steps but with the following hypotheses instead:
**Null hypothesis**: The missingness of avg ratings does not depend on the protein column

**Alternative hypothesis**: Null hypothesis: The missingness of avg ratings does depend on the protein column

**Test Statistic**: Absolute difference of means (avg amount of protein with rating missing - avg amount of minutes with protein not missing)

**Significance level**: 0.01

<iframe
  src="assets/fig_marSteps.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>


Interpretation: We ran the test by shuffling missingness 1000 times and we got a 130.29 observed difference which is shown on the graph. Since the p-value we got was (0) is less than our significance level (0.01), we **fail to reject the null hypothesis** thus the missingness of `avg_ratings` might be dependent on the `minutes` it takes to complete the recipe


## Hypothesis Testing
As mentioned in the introduction, we aim to determine whether cooking time is correlated with the average rating of recipes. Some people may assume that longer cooking times result in better flavors and higher ratings, while others may prefer quicker meals. To investigate this relationship, we conducted a permutation test. We first sorted the lengths of the cooking times into two groups, one long one short by determining if it was longer or shorter than the median cooking time. We then used the following hypotheses for our testing:

Null Hypothesis (H0): There is no relationship between cooking time and the average rating of recipes.

Alternative Hypothesis (Ha): There is a relationship between cooking time and the average rating of recipes.

Test Statistic: The difference in mean between longer .

Significance Level: 0.05

<iframe
  src="assets/fig_permTest.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Conclusion
Since the p-value is below 0.05, we **reject the null hypothesis**. This indicates that cooking time does not have a statistically significant effect on the average rating of recipes. 



## Framing a Prediction Problem



## Baseline Model

The baseline model for this project is a linear regression model aimed at predicting the average rating (`avg_rating`) of recipes based on two features:  

1. Cooking time bins (`cooking_time_bins`)
A categorical (nominal) feature that classifies recipes into four categories:
Short (<20 min)
Medium (20-60 min)
Long (60-120 min)
Extra Long (>120 min)
This feature was created by binning the `minutes` column using `pd.cut()`. One-hot encoding was applied to convert these categories into a format suitable for the regression model.  

2. Number of ingredients (`n_ingredients`)
A quantitative (continuous) feature representing the number of ingredients used in each recipe. This feature was standardized using `StandardScaler()` to ensure it had a mean of 0 and a standard deviation of 1.  

**Encoding and Transformations**  
- The nominal feature (`cooking_time_bins`) was transformed using **one-hot encoding** to create binary columns for each category.  
- The quantitative feature (`n_ingredients`) was standardized to account for different scales and improve model performance.  
- Any rows with missing target values (`avg_rating`) were removed to prevent errors during training.  

**Model Evaluation**  
After splitting the data into training (70%) and test (30%) sets, the pipeline was trained and evaluated. The baseline model achieved:  
Mean Squared Error: 0.41
R-squared: 0.00

**Model Quality**
While the MSE and R² provide a starting point, this baseline model is relatively simple and likely underfits the data. It does not capture complex relationships between features or interactions between cooking time, ingredient count, and other factors that might influence a recipe's rating. The next steps will involve exploring additional features, testing more complex models, and fine-tuning hyperparameters to improve predictive performance.  


## Final Model


## Fairness Analysis



