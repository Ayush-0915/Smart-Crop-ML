# 🌾 Smart Crop Advisory System

**Author:** Ayush Singh

Smart Crop Advisory System is a machine-learning project that combines crop recommendation, agricultural yield prediction, and profitability analysis into a single advisory workflow. Given soil, weather, location, season, and crop-year information, the system recommends a suitable crop and can estimate its expected yield and historical profitability for the selected state.

The project is implemented as a collection of Jupyter notebooks. The notebooks cover exploratory data analysis, model training, evaluation, model serialization, and integration of the trained models into one advisory function.

## Project Overview

Agricultural decisions depend on several interacting factors, including:

- Soil nutrient levels: nitrogen (N), phosphorus (P), and potassium (K)
- Temperature, humidity, rainfall, and soil moisture
- State, district, and growing season
- Historical crop production and yield
- Support price and cost of cultivation

Smart Crop uses these factors in three complementary modules:

1. **Crop recommendation**: recommends a crop from soil and climatic conditions.
2. **Yield prediction**: estimates crop yield using location, season, year, environmental conditions, and crop type.
3. **Profitability analysis**: reports support price, historical yield, cultivation cost, and estimated profit per hectare when matching historical data is available.

## Main Features

- Data exploration and preprocessing for agricultural datasets
- Crop classification from seven soil and weather features
- Yield regression using encoded categorical features and environmental measurements
- Profit calculation using historical agricultural economics data
- Saved scikit-learn models and label encoders for reuse
- Integrated `smart_crop_advisory` workflow
- Basic handling for unsupported locations, crops, and unavailable profitability records
- Visual analysis using distributions, correlation heatmaps, box plots, count plots, and model evaluation plots

## System Workflow

```text
Soil and weather inputs
        |
        v
Crop recommendation model
        |
        v
Recommended crop
        |
        +----------------------+
        |                      |
        v                      v
Yield prediction model    Profitability lookup
        |                      |
        +----------+-----------+
                   v
           Combined advisory
```

The integrated workflow is implemented in `notebooks/Intergration.ipynb`:

1. Load the trained crop recommendation and yield prediction models.
2. Load the encoders used during model training.
3. Recommend a crop from N, P, K, temperature, humidity, pH, and rainfall.
4. Convert the recommended crop into the format expected by the yield model.
5. Predict yield using state, district, crop year, season, temperature, humidity, and soil moisture.
6. Map the recommended crop to the profitability dataset where a compatible record exists.
7. Display the recommendation, predicted yield, support price, historical yield, and estimated profit per hectare.

## Repository Structure

```text
Smart Crop/
|
|-- Crop_recommendation.csv
|-- Models/
|   |-- crop_recommendation_model.pkl
|   |-- yield_prediction_model.pkl
|   |-- crop_encoder.pkl
|   |-- district_encoder.pkl
|   |-- label_encoder.pkl
|   |-- season_encoder.pkl
|   |-- state_encoder.pkl
|-- notebooks/
|   |-- crop_recommendation.ipynb
|   |-- yield_prediction.ipynb
|   |-- Profitability_Analysis.ipynb
|   |-- Intergration.ipynb
|-- .gitignore
|-- README.md
```

## Notebook Guide

### `crop_recommendation.ipynb`

This notebook trains and evaluates the crop recommendation classifier.

- Loads `Crop_recommendation.csv`
- Cleans column names and crop labels
- Checks missing values and duplicate rows
- Performs exploratory data analysis
- Uses the features `N`, `P`, `K`, `temperature`, `humidity`, `ph`, and `rainfall`
- Compares a Decision Tree classifier with a Random Forest classifier
- Evaluates accuracy, classification reports, and confusion matrices
- Examines feature importance
- Saves the selected crop recommendation model and target label encoder

### `yield_prediction.ipynb`

This notebook prepares a historical Indian agriculture dataset and trains a yield regression model.

- Loads the crop-yield dataset from the source URL used in the notebook
- Cleans categorical values and removes invalid production records
- Removes rows with missing production values
- Calculates yield as production divided by area
- Removes extreme yield values above the 95th percentile
- Encodes state, district, season, and crop values with `LabelEncoder`
- Uses a train/test split with `random_state=42`
- Evaluates regression performance with MAE, MSE, RMSE, and R-squared
- Saves the yield prediction model and categorical encoders

The yield model expects the following features:

```text
State_Name_encoded
District_Name_encoded
Season_encoded
Crop_encoded
Crop_Year
Temperature
Humidity
Soil_Moisture
```

### `Profitability_Analysis.ipynb`

This notebook analyzes historical crop profitability.

- Loads the profitability dataset from the source URL used in the notebook
- Standardizes long column names
- Calculates revenue per hectare
- Calculates profit per hectare
- Compares average profit by crop
- Trains and evaluates a Linear Regression model for cost of production analysis
- Provides a lookup function for crop and state combinations

The estimated profit is calculated as:

```text
Revenue per hectare = Support price x Historical yield
Profit per hectare = Revenue per hectare - Cost of cultivation (C2)
```

These values are historical estimates and should not be interpreted as guaranteed future returns.

### `Intergration.ipynb`

This notebook loads the saved artifacts and combines the three project modules. It defines:

- `recommend_crop(...)`
- `predict_yield(...)`
- `get_profit_estimate(...)`
- `smart_crop_advisory(...)`

The filename is currently spelled `Intergration.ipynb` in the repository and is retained as-is for consistency.

## Input Parameters

### Crop recommendation inputs

| Input | Description |
| --- | --- |
| `N` | Nitrogen content in the soil |
| `P` | Phosphorus content in the soil |
| `K` | Potassium content in the soil |
| `temperature` | Temperature associated with the field conditions |
| `humidity` | Relative humidity |
| `ph` | Soil pH value |
| `rainfall` | Rainfall measurement |

### Yield prediction inputs

| Input | Description |
| --- | --- |
| `state` | State name matching the training data |
| `district` | District name matching the training data |
| `year` | Crop year |
| `season` | Agricultural season matching the training data |
| `crop` | Crop name matching the training data |
| `temperature` | Temperature measurement |
| `humidity` | Humidity measurement |
| `soil_moisture` | Soil moisture measurement |

### Profitability inputs

| Input | Description |
| --- | --- |
| `crop` | Crop code/name available in the profitability data |
| `state` | State name available in the profitability data |

## Installation

The project requires Python 3.9 or newer and a Jupyter environment.

Install the main dependencies with:

```bash
python -m pip install pandas numpy matplotlib seaborn scikit-learn joblib jupyter
```

Alternatively, use the Python environment configured for the workspace in VS Code and install the same packages into that environment.

## Running the Project

1. Clone the repository and open the project folder in VS Code.
2. Select a Python interpreter with the required packages installed.
3. Open the notebooks in the `notebooks/` directory.
4. Run the training notebooks if you need to recreate the saved models.
5. Open `notebooks/Intergration.ipynb` to run the complete advisory workflow.
6. Execute the integration cells in order before calling `smart_crop_advisory(...)`.

Example:

```python
smart_crop_advisory(
    N=90,
    P=42,
    K=43,
    temperature=21,
    humidity=82,
    ph=6.5,
    rainfall=203,
    state="Andhra Pradesh",
    district="ANANTAPUR",
    year=2023,
    season="Kharif",
    soil_moisture=45
)
```

Expected output includes:

- Recommended crop
- Predicted yield, when the location and crop are supported by the encoders
- Support price, when a matching profitability record exists
- Historical yield
- Estimated profit per hectare

## Model Artifacts

The `Models/` directory contains serialized artifacts generated with `joblib`:

| File | Purpose |
| --- | --- |
| `crop_recommendation_model.pkl` | Crop classification model |
| `yield_prediction_model.pkl` | Crop yield regression model |
| `label_encoder.pkl` | Converts crop recommendation labels to crop names |
| `state_encoder.pkl` | Encodes state names for yield prediction |
| `district_encoder.pkl` | Encodes district names for yield prediction |
| `season_encoder.pkl` | Encodes season names for yield prediction |
| `crop_encoder.pkl` | Encodes crop names for yield prediction |

The encoders must remain compatible with the models that were trained with them. Do not replace an encoder independently unless the yield model is retrained as well.

## Data Sources

The repository includes `Crop_recommendation.csv` for crop recommendation training.

The yield prediction and profitability notebooks load additional datasets from public raw GitHub URLs during execution. Running those notebooks therefore requires internet access unless the datasets are downloaded and stored locally first.

The external datasets used by the notebooks are:

- Crop yield prediction dataset from `AbhishekKandoi/Crop-Yield-Prediction-based-on-Indian-Agriculture`
- Crop yield and profitability dataset from `shreyzo/Crop-yield-and-profitability-prediction`

## Important Implementation Notes

- Run notebooks with the project root as the working directory when possible.
- File paths should use the repository folder name `Models/`. On case-sensitive systems, `Models/` and `models/` are different directories.
- The notebooks currently contain some machine-specific absolute Windows paths in training cells. Replace those paths with relative paths before moving the project to another machine.
- The yield prediction function returns `None` when a state, district, season, or crop is not present in the corresponding encoder.
- Profitability is only available for crops covered by the mapping and for crop/state combinations found in the historical profitability dataset.
- Model predictions are advisory estimates and should be validated against local agronomic conditions, current market prices, and expert guidance.

## Limitations

- The system is not connected to live weather, soil sensors, market prices, or government support-price APIs.
- The recommendation model is trained on a fixed dataset and may not generalize to every soil type, climate, or region.
- Historical profitability values may not reflect current input costs, market prices, subsidies, or local farming practices.
- Unknown categorical values cannot be encoded by the saved `LabelEncoder` objects.
- There is currently no web, mobile, or REST API interface; the primary interface is the Jupyter notebook workflow.
- The integration currently provides profitability only for mapped crops with matching historical records.

## 🎯 Learning Outcomes
Through this project, I gained practical experience in:

-Machine Learning
-Data Preprocessing
-Feature Engineering
-Regression
-Classification
-Model Evaluation
-Feature Importance Analysis
-Data Integration
-Error Handling
-Building End-to-End Machine Learning Projects
