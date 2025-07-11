# Used Cars EDA And Price Prediction 🚗💰

## Project Overview

This project includes EDA and also aims to develop a robust machine learning model to accurately predict the selling price of used cars. Accurate pricing is crucial for businesses in the automotive industry, enabling better inventory management, competitive pricing strategies, and optimized profitability. This solution leverages advanced regression techniques and a systematic hyperparameter tuning approach to achieve high predictive performance.

The project encompasses data understanding, feature engineering, comparative model training, detailed hyperparameter tuning (coarse-to-fine) and comprehensive performance evaluation.

## Dataset

The dataset contains 8,128 used car listings with various features that influence selling price.

**Key Features Include:**

  * `name`: Name of the car (e.g., model, manufacturer)
  * `year`: Manufacturing year of the car
  * `selling_price`: The target variable - the price at which the car was sold (in Rupees)
  * `km_driven`: Total kilometers the car has driven
  * `fuel`: Type of fuel used by the car (e.g., Petrol, Diesel)
  * `seller_type`: Type of seller (e.g., Individual, Dealer)
  * `transmission`: Transmission type (Manual, Automatic)
  * `owner`: Number of previous owners
  * `mileage`: Fuel efficiency (kmpl or km/kg)
  * `engine`: Engine displacement (CC)
  * `max_power`: Maximum power generated by the engine (bhp)
  * `torque_nm`: Torque in Newton-meters
  * `torque_rpm_avg`: Torque in Revolutions Per Minute

### Data Insights:

  * **Target Variable Skewness:** The `selling_price` is highly right-skewed, ranging from 29,900 to 1 Crore Rupees, with a mean of approximately 6.38 Lakhs and a median of 4.50 Lakhs. This skewness was a key consideration for model training, leading to `log1p` transformation of the target.
  * **Feature Distributions:** Features like `km_driven`, `engine`, `max_power`, `torque_nm`, and `torque_rpm` also exhibited skewed or multimodal distributions.
  * **Missing Data:** Initial inspection revealed missing values in columns such as `mileage`, `engine`, `max_power`, `torque_nm`, and `torque_rpm`. These were addressed during the preprocessing stage.
  * **Correlations:** `selling_price` showed a positive correlation with `year` and `max_power`, as expected (newer cars and more powerful cars tend to be more expensive).

## Methodology

The project followed a structured machine learning workflow:

1.  **Data Loading & Initial Exploration:** Loading the dataset and performing initial checks for data types, missing values, and summary statistics.
2.  **Exploratory Data Analysis (EDA):** Visualizing distributions of key features and the target variable, identifying correlations, and understanding relationships between variables.
3.  **Data Preprocessing:**
      * Handling missing values (e.g., imputation).
      * Encoding categorical features (e.g., One-Hot Encoding).
      * **Target Transformation:** Applying `np.log1p` to the `selling_price` to handle its right-skewed distribution, improving model stability and performance.
4.  **Model Selection:** Three regression models were chosen for evaluation:
      * Decision Tree Regressor
      * Random Forest Regressor
      * Gradient Boosting Regressor
5.  **Iterative Model Training & Hyperparameter Tuning:**
    For each model, a systematic **coarse-to-fine tuning strategy** was implemented:
      * **Baseline Model:** Initial training with default parameters to establish a performance benchmark.
      * **Coarse Tuning (`RandomizedSearchCV`):** A broad search over a wide range of hyperparameters using `RandomizedSearchCV` with cross-validation. This step efficiently identifies promising regions in the parameter space. `n_iter_no_change` and `validation_fraction` parameters were used for early stopping within `GradientBoostingRegressor` to optimize training time and prevent overfitting during the search.
      * **Fine Tuning (`GridSearchCV`):** A more granular search over a narrower, optimized range of hyperparameters around the best values found during coarse tuning, using `GridSearchCV` with cross-validation.
      * **Note:** For Random Forest and Gradient Boosting Regressor, the fine-tuning phase did not yield significant improvements over the initial values obtained from the coarse tuning, indicating that the `RandomizedSearchCV` was highly effective in identifying near-optimal parameter regions early on.
6.  **Model Evaluation:** Performance was assessed using:
      * **R-squared (R2 Score):** Measures the proportion of variance in the dependent variable that can be predicted from the independent variables.
      * **Root Mean Squared Error (RMSE):** Measures the average magnitude of the errors, where errors are squared before they are averaged. Punishes larger errors more.
      * **Mean Absolute Error (MAE):** Measures the average magnitude of the errors, without considering their direction. Less sensitive to outliers than RMSE.
      * All absolute error metrics (RMSE, MAE) were calculated after inverse transforming the predictions back to the original price scale for practical interpretation.

## Results & Performance

| Model                           | Training R2 | Test R2    | Test RMSE (Rupees) | Test MAE (Rupees) |
| :------------------------------ | :---------- | :--------- | :----------------- | :---------------- |
| Decision Tree Regressor (Tuned) | 0.9906      | 0.9242   | 186,043       | 82,900       |
| Random Forest Regressor (Tuned) | 0.9879     | 0.9640   | 126,130      | 66,397      |
| **Gradient Boosting Regressor (Optimized)** | **0.9971** | **0.9634** | **124,403** | **64,133** |

### Key Findings:

  * The **Decision Tree Regressor** showed signs of overfitting, with a substantial drop in R2 from training to test set, and higher absolute errors.
  * The **Random Forest Regressor** significantly improved generalization and reduced errors, validating the power of ensemble methods.
  * The **Gradient Boosting Regressor** emerged as the top performer. It achieved the highest training R2 (nearly perfect fit) and the lowest RMSE and MAE on the test set, while maintaining a very strong test R2, demonstrating excellent generalization capabilities.
  * The coarse-to-fine tuning approach effectively identified optimal hyperparameters for both Random Forest and Gradient Boosting Regressor, as subsequent fine-tuning yielded minimal further improvements.
  * Despite achieving high accuracy, the RMSE value being notably higher than MAE consistently indicates that all models still struggle more with predicting the exact values of very high-priced cars due to the inherent skewness and spread in the `selling_price` distribution.

### Final Model Performance (Gradient Boosting Regressor):

  * **R2 Score (Test Set):** 0.9634
  * **RMSE (Test Set):** 113,747 Rupees
  * **MAE (Test Set):** 63,353 Rupees

**Conclusion:** The Gradient Boosting Regressor model, leveraging a `log1p` transformed target and optimized through coarse-to-fine tuning, provides highly accurate price predictions for used cars. An average error (MAE) of \~64,133 Rupees against a median car price of 4.5 Lakhs (14.25% average percentage error at the median) is a strong result for this complex, real-world regression problem.

## Future Work

To further enhance this project and prepare it for a production environment:

  * **Advanced Feature Engineering:** Explore creating more sophisticated interaction terms or deriving features from car models/brands using external data.
  * **Outlier/Error Analysis:** Conduct a deeper dive into the specific predictions with the largest errors (residuals) to understand if common patterns exist, particularly for the high-value cars, which could inform targeted feature engineering or segmented modeling.
  * **Model Ensembling (Stacking/Blending):** Experiment with combining predictions from multiple diverse models (e.g., Random Forest, Gradient Boosting, potentially even a simple linear model) to leverage their individual strengths.
  * **Experiment with Other Boosting Libraries:** Explore optimized gradient boosting libraries like XGBoost, LightGBM, or CatBoost, which often offer performance gains and built-in features like advanced early stopping.
  * **Model Deployment:** Develop an API (e.g., using Flask or FastAPI) to serve the model predictions and potentially containerize the application using Docker for easier deployment.
  * **Continuous Monitoring:** Implement a system to monitor the model's performance in a production environment and establish a retraining schedule to adapt to changing market conditions.

## Technologies Used

  * Python
  * Pandas (for data manipulation)
  * NumPy (for numerical operations)
  * Scikit-learn (for machine learning models and utilities)
  * Matplotlib (for visualizations)
  * Seaborn (for enhanced visualizations)
  * SciPy (for statistical distributions in `RandomizedSearchCV`)

## How to Run the Project (Example)

1.  **Clone the repository:**
    ```bash
    git clone https://github.com/your-username/your-repo-name.git
    cd your-repo-name
    ```
2.  **Create a virtual environment (recommended):**
    ```bash
    python -m venv venv
    source venv/bin/activate  # On Windows: venv\Scripts\activate
    ```
3.  **Install dependencies:**
    ```bash
    pip install -r requirements.txt
    ```
    (You'll need to create a `requirements.txt` file based on the libraries listed above).
4.  **Run the Jupyter Notebook:**
    ```bash
    jupyter notebook
    ```
    Open the `car_price_prediction.ipynb` (or similar name) notebook and run all cells.

-----
