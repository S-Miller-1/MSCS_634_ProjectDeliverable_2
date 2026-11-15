# MSCS 634 — Project Deliverable 2: Regression Modeling and Performance Evaluation

**Student:** Shawn Miller  
**Course:** MSCS 634 — Data Mining  
**Project:** Advanced Data Mining for Data-Driven Insights and Predictive Modeling

## About this project

For Deliverable 2, I kept working with the UCI Online Retail dataset from my first project. This time, I wanted to see how well regression models could estimate an invoice's total sales amount based on information about the items purchased.

I tried three models: a basic Ridge regression, a Ridge regression with more features, and a Random Forest. I compared their accuracy using several measures and checked how well they worked on later transactions.

## Dataset

The original dataset contains **541,909 rows and eight columns** of sales data from December 2010 through early December 2011. My cleaned dataset from Deliverable 1 contains **524,878 sales rows**. For this assignment, I grouped those rows into invoices, because I wanted to estimate the total amount of each order instead of the value of an individual product line.

I used the same cleaned file from Deliverable 1 and did not overwrite it.

**Dataset source:** Chen, D. (2015). *Online Retail*. UCI Machine Learning Repository. https://doi.org/10.24432/C5BW33

## Preparing the data and creating features

I grouped the sales rows by invoice and created new columns that could help the models. These included the number of products in an order, the total number of units, average and highest item prices, the month of the purchase, the day of the week, and the country.

I used three features in the basic Ridge model and more features in the other two models. This let me check whether adding information actually improved the predictions. I kept invoice numbers and calculated sales totals out of the predictors and handled missing values and other preparation steps inside each model's pipeline.

There is an important limit to this approach: the quantities and prices are already closely related to an invoice's total. The models are estimating the value of an order using details from that order, **not** predicting how much someone will spend before shopping.

## Models and testing

I used three regression models:

- **Basic Ridge regression:** number of order lines, total units, and average item price.
- **Ridge regression with extra features:** the same three features plus product, time, and country information.
- **Random Forest:** the expanded feature set, but with a different approach that can pick up more complicated patterns.

I trained the models on the earlier **80%** of invoices and tested them on the most recent **20%**. This gave me **15,968 training invoices** and **3,992 test invoices**, out of **19,960 invoices** total. I also used five-fold time-series cross-validation on the training portion to check whether the results stayed reasonably consistent across different periods.

For the evaluation, I used MAE, MSE, RMSE, and R². MAE tells me the average size of an error, while RMSE gives more weight to especially large mistakes. R² measures how much of the variation in invoice totals the model explains. I mainly used RMSE to compare overall performance, but I looked at the other scores too.

## Results

### Final test results

| Model | MAE (£) | MSE (£²) | RMSE (£) | R² |
|---|---:|---:|---:|---:|
| Ridge (basic) | 238.44 | 1,952,192.89 | **1,397.21** | **0.792** |
| Ridge (engineered) | 202.95 | 2,377,677.63 | 1,541.97 | 0.747 |
| Random Forest (engineered) | **129.72** | 5,303,897.88 | 2,303.02 | 0.435 |

The **basic Ridge model had the lowest RMSE and the highest R²** on the final test set. The model with extra features did slightly better on MAE, but it made some larger mistakes that pushed its RMSE higher. Random Forest had the lowest MAE, even though its RMSE was the highest. That difference showed me why I shouldn't rely on one score alone.

### Five-fold cross-validation results

| Model | Average CV RMSE (£) | RMSE standard deviation (£) | Average CV R² |
|---|---:|---:|---:|
| Ridge (basic) | 626.71 | 188.53 | 0.662 |
| Ridge (engineered) | 581.13 | 196.61 | 0.701 |
| Random Forest (engineered) | **468.37** | 194.99 | **0.789** |

Random Forest actually did best during cross-validation, when I tested it on several earlier periods. It just didn't hold up as well on the most recent test period. My main takeaway is that a model can look strong during training and still struggle when newer orders are different from what it has seen before.

## Checking unusually large transactions

One improvement I wanted to make after the instructor's feedback on Deliverable 1 was to look more closely at very large invoices. The top 1% started above **£4,821.17** in the full set of invoices. Those **200 invoices made up 19.37% of revenue**. That's a big enough share that they could change the overall results.

For the later test period, I used the 99th-percentile cutoff from the training data (**£4,235.77**) and found **89 out of 3,992 test invoices** above that amount. When I checked only the test invoices below that cutoff, RMSE was **£307.21** for basic Ridge, **£288.17** for engineered Ridge, and **£140.21** for Random Forest.

These lower error scores don't replace the full test results. They just show how much unusually large orders affected the comparison. I flagged these invoices for review rather than deleting them, because the dataset alone doesn't tell me whether a big order was valid or entered incorrectly.

## Charts and result files

The notebook creates three graphs: a comparison of RMSE and R², a chart of actual versus predicted invoice values, and a chart showing the spread of prediction errors. I included the graphs in `figures/` so they can be viewed without rerunning the notebook.

The project also includes `holdout_results.csv`, `cross_validation_results.csv`, and `outlier_sensitivity.csv`, which contain the actual evaluation numbers used in the discussion above.

## Challenges and what I learned

The biggest challenge was dealing with the wide range of invoice amounts. Most invoices were much smaller than a few unusually large orders, and those large orders had a major effect on RMSE. I learned that it helps to compare more than one metric and look at the data behind surprising results instead of assuming the model is simply good or bad.

I also learned that adding more features doesn't automatically lead to better predictions. The extra features helped Ridge in some ways, but the basic version still had the best RMSE on the latest test invoices. Comparing cross-validation with the final test made it easier to see where model performance changed over time.

If I continued this as a forecasting project, I would try to predict a customer's future spending using only earlier purchases. That would be more useful for planning than estimating an invoice amount after the order details are already known.

## How to run the project

1. Download or clone this repository.
2. Keep `Online_Retail_Cleaned.csv.zip` in the same folder as the notebook. It is the cleaned dataset from [Deliverable 1](https://github.com/S-Miller-1/S-Miller-1-MSCS_634_ProjectDeliverable_1).
3. Install the dependencies with `python -m pip install -r requirements.txt`.
4. Start Jupyter with `python -m notebook`.
5. Open `MSCS_634_ProjectDeliverable_2_Shawn.ipynb` and run all the cells in order.

The notebook will recreate the model results, exported CSV files, and graphs.

## Files included

| File | What it contains |
|---|---|
| `MSCS_634_ProjectDeliverable_2_Shawn.ipynb` | Code, charts, model comparisons, and explanations |
| `README.md` | Project summary, findings, and instructions |
| `requirements.txt` | Python libraries needed to run the notebook |
| `Online_Retail_Cleaned.csv.zip` | The cleaned dataset from Deliverable 1 |
| `holdout_results.csv` | Test-set scores for all three models |
| `cross_validation_results.csv` | Five-fold cross-validation scores |
| `outlier_sensitivity.csv` | Comparison with and without the largest invoices |
| `figures/` | Three charts created by the notebook |

## Reference

Chen, D. (2015). *Online Retail*. UCI Machine Learning Repository. https://doi.org/10.24432/C5BW33
