# Part B: Business Case Analysis


## B1. Problem Formulation

### (a) Machine Learning Problem Formulation
This problem can be formulated as a **supervised regression problem**.

- **Target Variable:** items_sold  
- **Input Features:** promotion_type, location_type, store_size, competition_density, is_weekend , is_festival  

- **Justification:** The goal is to predict the number of items sold, which is a continuous numerical value. Since we are predicting a quantity and not a category, regression is the appropriate approach. This allows the business to estimate expected sales for each promotion and choose the best one.

---

### (b) Rationale for Target Variable Selection

Although the company currently focuses on total revenue, using **items_sold** is more reliable for this problem.

Revenue can be influenced by many external factors such as product pricing, product mix, or inflation. For example, a store may generate high revenue by selling a few expensive items, even if the promotion itself was not very effective.

On the other hand, **items_sold directly reflects how well a promotion drives customer purchases**. It focuses purely on volume and removes pricing-related noise, making it easier to compare promotion performance across stores.

**Broader Principle:**  
In machine learning, the target variable should be closely related to what we are trying to influence. Choosing a variable that is directly affected by the input features leads to more reliable and meaningful predictions.

---

### (c) Proposed Modeling Strategy

Instead of one global model for all 50 stores, a better approach is to train **separate models per location type** — Urban, Semi-urban, and Rural.

A single model gets pulled in too many directions at once. Urban and rural customers likely respond very differently to the same promotion — a BOGO deal might work well in a high-footfall urban store but do little in a rural one where price sensitivity and competition density are quite different. Merging all stores together causes the model to produce predictions that don't work particularly well for any specific group.

Training separate models per location type keeps each model focused on stores that actually behave similarly, leading to more accurate predictions and genuinely actionable recommendations.


## B2. Data and EDA Strategy

### (a) Data Integration and Dataset Design

To prepare the modelling dataset, I would use the transactions table as the base.

- Join store attributes using store_id to add store-level information such as size and location.
- Join promotion details using promotion_type to include the type of promotion applied.
- Join the calendar table using transaction_date to include features like is_weekend and is_festival.

The grain of the final dataset would be: one row per store per day.

If the data is at transaction level, I would aggregate it by:
- Summing items_sold per store per day  
- Counting number of transactions per day  

This ensures each row represents the total daily performance of a store.

---

### (b) EDA Strategy

Before building the model, I would perform the following analyses:

1. Box plot of items_sold by promotion_type  
   - Purpose: To compare how different promotions perform  
   - Impact: Helps identify which promotions are more effective and whether interactions are needed  

2. Time-series plot of total sales  
   - Purpose: To observe trends and spikes during weekends or festivals  
   - Impact: Confirms whether time-based features are important  

3. Heatmap of location_type vs promotion_type (average sales)  
   - Purpose: To check if certain promotions work better in certain locations  
   - Impact: Supports decision to use segmented models  

4. Scatter plot of competition_density vs items_sold  
   - Purpose: To check relationship between competition and sales  
   - Impact: Helps decide if transformation or non-linear models are needed  

5. Distribution of items_sold  
   - Purpose: To detect outliers and understand spread  
   - Impact: Helps in deciding whether to transform or cap values  

---

### (c) Handling Promotion Imbalance

If 80% of transactions have no promotion active, the model risks becoming biased toward predicting baseline sales. It may learn to rely heavily on features like `store_size` or `location_type` while treating `promotion_type` as relatively unimportant — which is the opposite of what the business needs.

To address this:
- **Weighted training:** Assign higher weights to promotion records so the model is forced to learn from them more carefully
- **Stratified sampling:** Ensure each promotion type is proportionally represented in both training and test sets
- **Baseline feature:** Create a feature capturing average store sales without any promotion, so the model learns the incremental lift caused by each promotion rather than just total volume
- **Separate evaluation:** Assess performance specifically on promotion records to confirm the model captures the promotional effect, not just overall accuracy


## B3. Model Evaluation and Deployment

### (a) Evaluation Strategy and Metrics

Since the data is time-based, I would use a **temporal split to simulate real-world forecasting**, where earlier data is used for training and the most recent data is used for testing (for example, first 30 months for training and last 6 months for testing).

A random split is not suitable because retail data is chronological. If we randomly mix the data, the model may learn from future patterns while predicting past data, which leads to data leakage and unrealistic results.

For evaluation, I would use:

- **MAE (Mean Absolute Error):** Shows how many items the prediction is off on average. Easy to communicate to the marketing team — for example, "the model is typically off by around 15 items per store per day."

- **RMSE (Root Mean Squared Error):** Penalises large errors more heavily than MAE. In retail, a big miss in either direction — a stockout or significant overstock — is far more costly than a small one, so RMSE captures the kind of errors that actually matter to the business.

---

### (b) Investigating Model Recommendations

To understand why Store 12 gets different recommendations in December and March, I would use **feature importance** and **SHAP values**.

These help explain how different features affect the prediction.

For example:
- In December, the model may give more importance to `is_festival`, which increases customer activity and makes Loyalty Points more effective.
- In March, when there are no festivals, factors like `competition_density` may become more important, making discounts more effective.

To explain this to the marketing team, I would present a simple comparison showing which features influenced the prediction in each case, instead of using complex technical details.

---

### (c) End-to-End Deployment Process

After training the model, I would save the trained pipeline using joblib (or pickle) so that both the preprocessing steps and the model can be reused without retraining.

At the start of each month:
- Collect the latest store data and upcoming calendar information  
- Create multiple scenarios for each store (one for each promotion type)  
- Pass these inputs to the model to get predicted sales  
- Select the promotion with the highest predicted items_sold  

For monitoring:
- Compare predicted sales with actual sales every month  
- Track errors over time using metrics like MAE  
- If performance drops consistently, retrain the model using recent data  

This ensures the model stays accurate and adapts to changing customer behaviour.