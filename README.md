# MSCS 634 — Project Deliverable 2
**Student:** Shawn Miller  
**Course:** MSCS 634 — Data Mining  
**Topic:** Regression Modeling and Performance Evaluation

## Project Overview
For this part of the project, I used the same UCI Online Retail dataset from Deliverable 1 to estimate invoice totals. The original dataset had 541,909 records, and the cleaned version has 524,878 sales records. I grouped the cleaned data into 19,960 invoices before building the models.

## Feature Engineering and Models
I created invoice-level features such as the number of products, quantity purchased, average and maximum prices, purchase month, and country. I compared three models: basic Ridge Regression, Ridge Regression with additional features, and Random Forest Regression. I used the earlier 80% of invoices for training and the latest 20% for testing, along with five-fold time-series cross-validation.

## Model Evaluation
The models were evaluated using MAE, MSE, RMSE, and R². Here are the final test results:

| Model | MAE (£) | RMSE (£) | R² |
|---|---:|---:|---:|
| **Ridge (basic)** | 238.44 | **1,397.21** | **0.792** |
| Ridge (engineered) | 202.95 | 1,541.97 | 0.747 |
| Random Forest | **129.72** | 2,303.02 | 0.435 |

Basic Ridge had the lowest RMSE and highest R² on the final test data. Random Forest had the lowest MAE and performed best during cross-validation (average RMSE of £468.37), but it struggled more with some large invoices in the latest test period. This showed me why checking multiple metrics and different time periods matters.

## Challenges and What I Learned
One challenge was dealing with unusually large orders. The largest 1% of invoices accounted for about 19.37% of revenue, so those orders had a big effect on the error scores. I checked the results with and without extreme test invoices, but I did not automatically remove them because I couldn't confirm they were errors. I also found that adding more features did not always improve a model. These models estimate invoice totals using the items already purchased; they do not predict future spending before an order happens.

## Files and How to Run
The Jupyter Notebook (`MSCS_634_ProjectDeliverable_2_Shawn.ipynb`) contains the code, comments, evaluation, and chart-generation steps. The three CSV result files contain the holdout, cross-validation, and outlier comparisons. Charts are generated into the `figures/` folder when the notebook runs.

To run it, place `Online_Retail_Cleaned.csv.zip` from [Deliverable 1](https://github.com/S-Miller-1/S-Miller-1-MSCS_634_ProjectDeliverable_1) next to the notebook, install the libraries with `python -m pip install -r requirements.txt`, and run the notebook cells in order.

## Dataset Reference
Chen, D. (2015). *Online Retail*. UCI Machine Learning Repository. https://doi.org/10.24432/C5BW33
