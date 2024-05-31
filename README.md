# Time Series Analysis of Air Quality in Bishkek

## Project Overview
This project performs a time series analysis of PM2.5 air quality data in Bishkek using Autoregressive (AR) models. The goal is to explore, analyze, and predict PM2.5 levels to understand the air quality trends and patterns over multiple years.

## Table of Contents
- [Introduction](#introduction)
- [Data](#data)
- [Exploratory Data Analysis](#exploratory-data-analysis)
- [Modeling](#modeling)
- [Evaluation](#evaluation)
- [Results](#results)
- [Conclusion](#conclusion)
- [Future Work](#future-work)

## Introduction
Air quality is a significant environmental health concern, especially in urban areas like Bishkek. This project aims to analyze the historical PM2.5 data to identify trends, seasonal patterns, and predict future air quality levels using time series models.

## Data
The data used in this project includes PM2.5 measurements from 2019 to 2024 in Bishkek. The datasets are sourced from local air quality monitoring stations.

## Exploratory Data Analysis
The exploratory data analysis (EDA) phase includes:
- Visualizing PM2.5 levels by different AQI categories.
- Analyzing the distribution of PM2.5 levels across months and seasons.
- Identifying outliers and resampling the data for time series analysis.
- Generating various plots such as violin plots, box plots, histograms, lag plots, and autocorrelation plots.

## Modeling
We employed an Autoregressive (AR) model to forecast PM2.5 levels. The steps involved:
1. **Baseline Model**: Using the mean of the training data as a baseline.
2. **AR Model**: Training an AR model with optimal lags.
3. **Walk-Forward Validation**: Improving the model by retraining it iteratively for each new observation.

## Evaluation
The model's performance was evaluated using the Mean Absolute Error (MAE). The traditional split and walk-forward validation methods were used to assess the generalization of the model.

## Results
- The baseline model resulted in a high MAE.
- The AR model with walk-forward validation significantly improved the predictions with a lower MAE.
- Visualizations of predictions against actual values showed the model's effectiveness in capturing the trends.

## Conclusion
This project demonstrates the application of time series analysis to forecast air quality levels in Bishkek. The AR model with walk-forward validation provided more accurate predictions compared to traditional methods.

## Future Work
- Incorporate more advanced models like ARIMA or machine learning techniques.
- Analyze the impact of external factors such as weather conditions on PM2.5 levels.
- Extend the analysis to other pollutants and cities.
