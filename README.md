# Predicting User Disengagement Risk in SaaS
## A Case Study for B2B SaaS companies like File.ai

## Project Overview
For modern SaaS companies, especially in the B2B space, customer retention is paramount. The biggest threat is not explicit cancellation, but silent churn where users slowly disengage, fail to activate key features, and eventually stop logging in. This project demonstrates a complete, end-to-end machine learning framework designed to proactively identify users at high risk of disengagement.
The goal is to boost user activation, increase long-term retention, and maximize customer lifetime value. While this proof-of-concept is built on a publicly available dataset, the methodology is directly applicable to the event-driven data of a company like **File.ai**.

## Table Of Content
  - [Project Goals](#project-goals)
  - [Data source](#data-source)
  - [Tools](#tools)
  - [Data Cleaning and Preparation](#data-cleaning-and-preparation)
  - [Exploratory Data Analysis](#exploratory-data-analysis)
  - [Data Analysis and Visualisation](#data-analysis-and-visualisation)
  - [Feature Engineering for Predictive Modeling](#feature-engineering-for-predictive-modeling)
  - [Model Evaluation](#model-evaluation)
  - [Recommendation](#recommendation)
  - [Limitations](#limitations)
  - [Conclusion](#conclusion)

### Project Goals
The primary goal is to build and validate a machine learning model that predicts an "Engagement Risk Score" for individual users. This enables a customer success team to:
* Prioritize outreach to users who are showing early signs of disengagement.
* Understand the key user behaviors and feature adoption patterns that correlate with long-term retention by develop strategies driven by insights to improve user onboarding

### Data source
To demonstrate this predictive framework, this project utilizes the well-known Telco Customer Churn dataset from Kaggle as a proxy for typical SaaS user activity data. This dataset, while from the telecom industry, contains analogous features that serve as excellent stand-ins for key SaaS metrics:
* tenure: Directly analogous to user lifetime or time since signup.
* Contract: Represents the user's commitment level (e.g., Monthly vs. Annual SaaS plan).
* Service Subscriptions (e.g., OnlineSecurity, TechSupport): Act as proxies for the adoption of specific, "sticky" features within a SaaS product.
* Churn: Serves as our target variable, representing a user becoming fully disengaged.

In a real-world implementation for File.ai, this proxy data would be replaced with actual user event logs from their product database (e.g., time_since_last_login, number_of_documents_uploaded, number_of_queries_asked).

### Tools

* **Excel:** Data cleaning and preparation.
* **[SQL Server](data_manipulation.csv):** Data validation and exploratory data analysis.
* **[R](telcom_churn_analysis.R):**  In-depth exploratory data analysis, and visualization.
* **[Tableau](https://public.tableau.com/app/profile/ayomide.fase2159/viz/TelcomChurn_17360950840870/Dashboard1):** Interactive dashboard creation.
* **[Python:](telco_customer_churn_prediction.ipynb)**
  - Libraries: Pandas, NumPy for data manipulation.
  - Libraries: Matplotlib, Seaborn for visualization.
  - Libraries: Scikit-learn for feature engineering, model building (Decision Tree, Random Forest), hyperparameter tuning (GridSearchCV), and evaluation.
  - Libraries: XGBoost for building the gradient boosting model.
  - Libraries: Pickle for saving the final model.
 

### Exploratory Data Analysis (EDA) - Understanding Engagement Drivers
A thorough EDA was conducted to identify the key factors correlated with user disengagement in the proxy dataset.
Key Findings:
* User Lifetime (tenure): Newer users (0-12 months) have a significantly higher disengagement rate (47.7%) compared to long-term users (61-72 months), who have a very low rate (6.6%). This highlights the critical importance of the early onboarding phase.
* Commitment Level (Contract): Users on short-term, 'Month-to-month' plans are far more likely to disengage (42.7%) than those on annual or multi-year plans (<12%).
* Core Feature Adoption (InternetService): Users of the premium 'Fiber optic' service showed a surprisingly high disengagement rate (41.9%). For File.ai, this is a powerful analogy for a scenario where users adopt a premium feature but churn due to potential issues with pricing, usability, or not seeing the expected value.
* Value-Added Features (OnlineSecurity, TechSupport, etc.): Users who do not adopt these optional "sticky" features disengage at a much higher rate (~41%) than those who do (~15%). This suggests that guiding users to adopt a suite of features is key to retention.

### Feature Engineering for Predictive Modeling
To enhance the model's predictive power, several features were engineered:
* Number_of_Optional_Services: A numerical feature was created to count the number of key value-added services each user subscribes to. This acts as a powerful, concise "feature adoption score."
* One-Hot Encoding: All categorical features were converted into a numerical format using one-hot encoding to make them suitable for the machine learning algorithms.

### Model Evaluation
After rigorous tuning and comparison, the XGBoost Classifier was selected as the final model due to its superior balance of performance metrics.
Final XGBoost Model Performance on Unseen Test Data:
| Metric               | Score  | Interpretation for File.ai                                                                                                          |
| Recall (Sensitivity) |	80.0% |	The model successfully identifies 80% of all users who are truly at risk of disengaging. This is a huge win for proactive outreach. |
| F1-Score             |	63.0% |	A strong balance between correctly identifying at-risk users (Recall) and not flagging too many safe users (Precision).             |
| Precision            |	52.0% |	When the model flags a user as "at-risk," it is correct about 52% of the time. This helps focus retention efforts.                  |
| ROC AUC Score        |	77.0% |	Indicates good overall discriminatory power, meaning the model is effective at separating at-risk users from engaged users.         |
