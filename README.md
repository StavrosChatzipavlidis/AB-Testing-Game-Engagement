# A/B Testing for Mobile Game Retention

## Introduction to A/B Testing

A/B testing, also known as split testing, is a method of comparing two versions of a webpage, app, or product to determine which one performs better. This involves splitting users into two groups: Group A, which uses the original version (control group), and Group B, which uses a modified version (treatment group). The performance of both versions is then measured using key metrics to identify which version yields better results.

### Key Concepts
- Control Group: The group that experiences the original version.
- Treatment Group: The group that experiences the modified version.
- Metrics: Quantitative measures used to compare the performance of the two versions (e.g., click-through rate, conversion rate, retention rate).

## Project Overview

This project aims to conduct an A/B test on a mobile app to evaluate the retention rates of two different versions over a period of one week. Specifically, we are interested in retention on Day 1 and Day 7 after the initial use of the app. The app has been modified to create two different experiences, and we want to determine which version keeps users engaged longer.

### Goals

- Compare Day 1 retention rates between Group A and Group B.
- Compare Day 7 retention rates between Group A and Group B.
- Analyze statistical significance to determine if differences in retention rates are meaningful.

## Data Import and Initial Exploration

1. Setup and Import Libraries

First, ensure you have the required libraries installed. You can do this using pip:

```python
pip install pandas numpy matplotlib plotly seaborn scipy
```

2. Load and Inspect Data

We load the dataset using pandas and perform some initial checks:

- Check Data Types: Ensure each column has the correct data type.
- Check for Duplicates: Verify that there are no duplicate user IDs.
- Check for Negative Values: Ensure there are no negative values in the sum_gamerounds column.
- Check for Missing Values: Confirm that there are no missing values in the dataset.

### Summary of Initial Data Exploration

- Data Types: Confirmed all columns have appropriate data types.
- Duplicate Check: No duplicate user IDs were found.
- Negative Values: No instances with negative game rounds were detected.
- Missing Values: No missing values were found.

## Summary Statistics

Next, we calculate the summary statistics for the sum_gamerounds column to understand the distribution of game rounds played by users.

```python
percentiles = [0.01, 0.05, 0.1, 0.2, 0.5, 0.8, 0.9, 0.95, 0.99]
summary_stats = game_data['sum_gamerounds'].describe(percentiles=percentiles).transpose()

print("Summary statistics for gamerounds:")
print(summary_stats)
```
Output:

![Summary](./img/summary.png)


### Summary of Summary Statistics
- Count: The dataset contains 90,189 observations.
- Mean: On average, players engage in approximately 51 game rounds.
- Standard Deviation: There is significant variability in game rounds, as indicated by a standard deviation of 195.05 rounds.
- Percentiles:
    - 1% of players engaged in 0 rounds.
    - 5% of players engaged in at least 1 round.
    - 20% of players engaged in 3 or more rounds.
    - 50% (median) of players engaged in 16 rounds.
    - 80% of players engaged in 67 or more rounds.
    - 95% of players engaged in up to 221 rounds.
    - 99% of players engaged in up to 493 rounds.
- Outliers: A few outliers played as many as 49,854 rounds.
