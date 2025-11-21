# Original source
https://github.com/gouravshah/hosue-price-predictor

# Environment setup.
uv venv --python python3.14
source .venv/bin/activate
uv pip install -r requirements.txt

# Run jupyterlab web app.
python -m jupyterlab

# Run Data Engineering script.
python src/data/run_processing.py

# Run Features Engineering script.
python src/features/engineer.py --input data/processed/cleaned_house_data.csv --output data/processed/featured_house_data.csv --preprocessor models/trained/preprocessor.pkl

# Modeling
python src/models/train_model.py --config configs/model_config.yaml --data data/processed/featured_house_data.csv --models-dir models --mlflow-tracking-uri http://localhost:5555

# Run MLflow
docker-compose -f deployment/mlflow/docker-compose.yaml up -d

# Build docker images and run
docker image build -t hpp-fastapi .
docker image history hpp-fastapi:latest
docker run -idtP hpp-fastapi:latest
docker run --rm -it hpp-fastapi:latest bash


# curl command for testing
curl -X POST "http://localhost:55000/predict" \
-H "Content-Type: application/json" \
-d '{
  "sqft": 1500,
  "bedrooms": 3,
  "bathrooms": 2,
  "location": "suburban",
  "year_built": 2000,
  "condition": "fair"
}'
