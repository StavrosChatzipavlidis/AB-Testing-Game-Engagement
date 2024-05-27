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

## Summary Statistics for Game Rounds Grouped by A/B Groups

The provided summary statistics give us insights into the distribution of game rounds played by users in the control group (gate_30) and the treatment group (gate_40).

```python
group_stats = game_data.groupby("version")['sum_gamerounds'].agg(
    count=lambda x: len(x),
    median=lambda x: np.median(x),
    mean=lambda x: np.mean(x),
    std=lambda x: np.std(x),
    max=lambda x: np.max(x)
).reset_index()

colors = {'gate_30': 'lightblue', 'gate_40': 'lightcoral',}

plt.figure(figsize=(12, 2))
plt.axis('off')
table = plt.table(cellText=group_stats.values,
                  colLabels=group_stats.columns,
                  cellLoc='center',
                  rowLoc='center',
                  loc='center',
                  cellColours=[[colors.get(version, 'lightgrey')] * len(group_stats.columns) for version in group_stats['version']])

table.auto_set_font_size(False)
table.set_fontsize(10)

plt.title("Summary statistics by A/B groups for 'sum_gamerounds'", fontsize=14)

plt.show()
```

Output:
![SummaryAB](./img/summaryAB.png)

Conclusions:

- Median and Mean: Both groups have similar median and mean values, suggesting that typical user engagement (in terms of game rounds played) is not drastically different between the two versions.

- Variability: The control group (gate_30) shows much higher variability in user engagement. This could be due to a few highly engaged users who play a large number of game rounds, which is reflected in the very high maximum value.

- Outliers: The presence of a user who played 49,854 rounds in the control group (gate_30) significantly impacts the standard deviation and maximum values. Such outliers can skew the data and should be considered when interpreting the results.


## Visualization of Game Rounds by Version

### Function to Create the Plot

The following function creates an interactive plot using Plotly to visualize the number of game rounds played by users, grouped by the version of the app. The plot includes dropdown menus to toggle between different versions or view all versions simultaneously.

<details>

```python
import plotly.graph_objects as go

def create_game_rounds_by_version_plot(game_data):
    traces = []
    for version in game_data['version'].unique():
        filtered_data = game_data[game_data['version'] == version]
        trace = go.Scatter(
            x=filtered_data.index,
            y=filtered_data.sum_gamerounds,
            mode='lines',
            name=version,
            legendgroup=version
        )
        traces.append(trace)

    layout = go.Layout(
        title='Game Rounds by Version',
        xaxis=dict(title='Index'),
        yaxis=dict(title='Sum Gamerounds'),
        legend=dict(x=1.1, y=1),
        updatemenus=[
            dict(
                buttons=[
                    dict(
                        label="All",
                        method="update",
                        args=[{"visible": [True] * len(traces)},
                              {"title": "Game Rounds by Version - All"}]
                    ),
                    *[
                        dict(
                            label=version,
                            method="update",
                            args=[{"visible": [trace.name == version for trace in traces]},
                                  {"title": f"Game Rounds by Version - {version}"}]
                        ) for version in game_data['version'].unique()
                    ]
                ],
                direction="down",
                pad={"r": 10, "t": 10},
                showactive=True,
                x=1,
                xanchor="right",
                y=1,
                yanchor="top"
            )
        ]
    )

    fig = go.Figure(data=traces, layout=layout)
    return fig
```
</details>