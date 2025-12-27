# Singapore MRT Flow Predictor

A machine learning-powered system that predicts crowd levels at Singapore MRT stations and provides real-time directional flow analysis.

## Overview

This project uses XGBoost and K-Means clustering to analyze Singapore's Mass Rapid Transit (MRT) passenger data and predict station crowding patterns. The system accounts for tidal flow patterns—the natural ebb and flow of commuters during peak hours—to provide intelligent travel recommendations.

## Features

- **Crowd Level Prediction**: Uses XGBoost regression to predict passenger density at any station and time
- **Directional Flow Analysis**: Distinguishes between inbound (towards CBD) and outbound (away from CBD) traffic
- **Tidal Flow Logic**: Automatically detects morning and evening rush hour patterns and adjusts predictions accordingly
- **Real-time Scheduling**: Generates 3-hour forecasts in 15-minute intervals
- **Terminus Station Detection**: Recognizes origin points (like Changi Airport, Marina South Pier) for more accurate predictions

## Dataset

The project uses Singapore MRT tap-in/tap-out transaction data:
- **Source**: Transport node train data (August-October 2023)
- **Format**: CSV files with monthly data
- **Fields**: Year-month, day type, time, station code, tap-in/tap-out volumes

## Project Structure

```
├── main.ipynb                          # Main Jupyter notebook
├── metro_kmeans.pkl                    # Trained K-Means clustering model
├── metro_xgboost.json                  # Trained XGBoost prediction model
├── station_names.json                  # List of valid station names
├── Names.json                          # Station code-to-name mapping
└── Dataset/
    ├── transport_node_train_202308.csv
    ├── transport_node_train_202309.csv
    └── transport_node_train_202310.csv
```

## Installation

### Requirements
- Python 3.8+
- pandas
- xgboost
- scikit-learn
- joblib

### Setup

```bash
# Clone the repository
git clone https://github.com/yourusername/mrt-flow-predictor.git
cd mrt-flow-predictor

# Install dependencies
pip install -r requirements.txt
```

## Usage

### Training the Model

Run the training pipeline in the notebook to generate trained models:

```python
# The notebook will:
# 1. Load all CSV files from the Dataset folder
# 2. Process and engineer features
# 3. Train K-Means clustering model
# 4. Train XGBoost regression model
# 5. Save models and station inventory
```

### Making Predictions

```python
# Run the prediction interface
python predict.py
# or execute the "Predict" cell in the notebook

# Follow the prompts:
# 1. Enter a valid station name
# 2. Select direction (1=Outbound, 2=Inbound)
# 3. View 3-hour crowd forecast in 15-minute intervals
```

## How It Works

### 1. Data Processing
- Maps station codes to station names
- Calculates derived features:
  - `day_of_week`: Weekday vs. weekend/holiday classification
  - `time_in_minutes`: Hour converted to minutes since midnight
  - `station_id`: Hash-based ID from station name
  - `total_crowd`: Sum of tap-in and tap-out volumes

### 2. Feature Engineering
- **Station ID**: Hashed identifier for station location
- **Time Features**: Hour-of-day and minute information
- **Day Type**: Binary encoding (0=Weekday, 5=Weekend)
- **Transport Type**: Placeholder for future expansion

### 3. Model Pipeline

**K-Means Clustering** (3 clusters):
- Cluster 0: Low crowd
- Cluster 1: Moderate crowd
- Cluster 2: High crowd/busy

**XGBoost Regression**:
- Predicts cluster membership for any station at any time
- Hyperparameters: max_depth=6, eta=0.1, 100 boosting rounds

### 4. Tidal Flow Logic

The system applies domain-specific rules to account for commute patterns:

**Morning Rush (6:00-10:00)**
- Outbound: EMPTY (commuters heading inbound to CBD)
- Inbound: High traffic

**Evening Rush (17:00-20:00)**
- Inbound: EMPTY (commuters heading outbound from CBD)
- Outbound: High traffic

**Off-Peak Hours**: Base predictions apply

## Output Format

```
TIME       | STATUS               | NOTES
-----------------------------------------------------------------
>> 08:10   | EMPTY                | Low Crowd
   08:25   | EMPTY                | Low Crowd
   08:40   | EMPTY                | Low Crowd
```

**Status Types**:
- `EMPTY`: Low passenger density
- `MODERATE`: Mid-range crowd levels
- `CROWDED`: High passenger density
- `FRESH TRAIN`: Departure from terminus station

## Model Performance

- **Training Data**: 500k+ MRT transactions across 3 months
- **Stations Covered**: 40+ MRT stations
- **Temporal Resolution**: 15-minute intervals, 24-hour cycle
- **Accuracy**: Cluster-based predictions with domain-specific adjustments

## Limitations

- Predictions based on historical patterns from Aug-Oct 2023
- Does not account for special events, holidays beyond dataset, or service disruptions
- Tidal flow rules are generalized and may vary by line
- Requires valid station names for predictions

## Future Improvements

- Line-specific tidal flow patterns
- Real-time GTFS integration
- Weather and special event adjustments
- Mobile app deployment
- Individual train capacity tracking
- Multi-station journey planning

## File Descriptions

| File | Purpose |
|------|---------|
| `metro_kmeans.pkl` | Trained K-Means model for crowd segmentation |
| `metro_xgboost.json` | XGBoost model for cluster prediction |
| `station_names.json` | List of all valid station names |
| `Names.json` | Mapping of station codes to English names |

## Contributing

Contributions are welcome! Areas for improvement:
- Enhanced tidal flow rules per line
- Real-time data integration
- UI/UX improvements
- Performance optimization

## Contact

For questions or suggestions, please open an issue on GitHub or contact the project maintainers.

---

**Built with ❤️ for Singapore commuters**
