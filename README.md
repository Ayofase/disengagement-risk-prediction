# Predicting User Disengagement Risk in SaaS
## A Case Study for B2B SaaS companies like File.ai

## Project Overview
For modern SaaS companies, especially in the B2B space, customer retention is paramount. The biggest threat is not explicit cancellation, but silent churn where users slowly disengage, fail to activate key features, and eventually stop logging in. This project demonstrates a complete, end-to-end machine learning framework designed to proactively identify users at high risk of disengagement.
The goal is to boost user activation, increase long-term retention, and maximize customer lifetime value. While this proof-of-concept is built on a publicly available dataset, the methodology is directly applicable to the event-driven data of a company like **File.ai**.

## Table Of Content
  - [Project Goals](#project-goals)
  - [Data source](#data-source)
  - [Tools](#tools)
  - [Exploratory Data Analysis](#exploratory-data-analysis)
  - [Feature Engineering for Predictive Modeling](#feature-engineering-for-predictive-modeling)
  - [Model Evaluation](#model-evaluation)
  - [Model Insights and Feature Importance](#model-insights-and-feature-importance)
  - [Business Recomendations](#business-recommendations)
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
 

### Exploratory Data Analysis 
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
|----------------------|--------|-------------------------------------------------------------------------------------------------------------------------------------|
| Recall (Sensitivity) |	80.0% |	The model successfully identifies 80% of all users who are truly at risk of disengaging. This is a huge win for proactive outreach. |
| F1-Score             |	63.0% |	A strong balance between correctly identifying at-risk users (Recall) and not flagging too many safe users (Precision).             |
| Precision            |	52.0% |	When the model flags a user as "at-risk," it is correct about 52% of the time. This helps focus retention efforts.                  |
| ROC AUC Score        |	77.0% |	Indicates good overall discriminatory power, meaning the model is effective at separating at-risk users from engaged users.         |


![xgb_confusion_matrix](https://github.com/user-attachments/assets/7854bc51-8ba6-44fb-93fe-207db33679c1)


### Model Insights and Feature Importance
The model's predictions are primarily driven by the following factors, providing actionable insights for File.ai:

* User Lifetime (tenure): The single most important predictor.
* Commitment Level (Contract): Users on short-term plans are a major flight risk.
* Core Service Type (InternetService_Fiber optic): Indicates that even users of premium features can be at high risk.
* num_optional_services: Our engineered feature proved highly valuable, showing that the breadth of feature adoption is key.
* Billing & Payment Methods: PaymentMethod_Electronic check was a surprisingly strong indicator of risk.

### Business Recommendations
The insights from both the exploratory data analysis and the final XGBoost model provide a clear, data-driven roadmap for improving user retention and maximizing customer lifetime value for a SaaS platform like File.ai. The following recommendations are based on the model's findings:

**1. Prioritize High-Risk User Segments with Proactive Outreach:**
The model can generate a daily or weekly list of users with the highest predicted disengagement risk. Customer Success teams should focus their efforts on these segments first:
* **New Users (0-12 Months):** This group has the highest risk. Implement an automated, triggered email sequence to guide them through key activation steps in their first 30 days.
* **Users on Monthly Plans (Contract: Month-to-month):** Offer a small discount (e.g., 10-15%) for switching to an annual plan to increase their commitment.
* **Users with Low Feature Adoption (num_optional_services):** Proactively send them tutorials, case studies, or invite them to a webinar showcasing the "sticky" features they aren't using.
  
**2. Address the "Premium Feature" Paradox:**
* **The Finding:** Our proxy data showed users of the premium 'Fiber Optic' service had a surprisingly high churn rate.
* **The Analogy for File.ai:** This is a critical warning sign for a potential scenario where users adopt an advanced, powerful feature (e.g., "cross-document Q&A" or "API access") but churn anyway.
* **Recommendation:** Use product analytics to investigate this segment. Are they struggling with the feature's complexity? Is the perceived value not matching the price point? Is it buggy? Proactive support and dedicated documentation for these power-user features are essential to retain high-value customers.

**3. Convert Model Insights into Targeted Marketing & Onboarding:**
* **Finding:** The PaymentMethod was a strong predictor.
* **Recommendation:** If certain payment methods correlate with lower risk (e.g., direct credit card vs. invoicing), guide new users towards those options during checkout.
* **Finding:** Users with dependents or partners (proxies for stable teams/households ) churned less.
* **Recommendation:** Frame marketing materials around team collaboration and stability. Encourage trial users to invite their team members to File.ai, as collaborative use is likely a strong retention driver.
  
**4. Implement a Data-Driven Feedback Loop:**
For users the model flags as high-risk, trigger an automated, non-intrusive survey (e.g., "On a scale of 1-10, how useful did you find File.ai this week?"). This can provide valuable qualitative data to understand why users are disengaging, complementing the model's who.
### Limitations

* **Proxy Data:** The model was built using a public telecom dataset. While the features serve as excellent analogies for SaaS metrics, a model trained on File.ai's actual user event data would be significantly more accurate and tailored.
* **Static Features:** The dataset is a static snapshot. A production model would be greatly enhanced by incorporating dynamic, time-series features (e.g., trends in query volume over the last 30 days).
* **External Factors:** The model does not account for external factors like competitor actions, pricing changes, or shifts in the user's business needs.
### Conclusion
This project successfully demonstrates a complete, end-to-end workflow for building a proactive user disengagement prediction model. By moving beyond simple analysis, we developed and tuned a robust XGBoost classifier capable of identifying 80% of at-risk users, providing a significant opportunity for targeted retention efforts.
The key takeaway is that user retention is driven by a combination of commitment level (contract type), user lifecycle stage (tenure), and, most importantly, feature adoption. The engineered num_optional_services feature proved to be a highly valuable predictor.
For a SaaS company like File.ai, implementing such a framework is not just about reducing churn, it's about systematically understanding user behavior, optimizing the onboarding journey, and building a more "sticky" product. By identifying and engaging at-risk users before they become inactive, this predictive model serves as a tool to enhance customer success and drive sustainable growth. Future work should focus on training this framework on real product usage data and integrating it into a live operational workflow.
