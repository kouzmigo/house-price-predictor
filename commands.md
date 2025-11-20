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

# Run MLflow
docker-compose -f deployment/mlflow/docker-compose.yaml up -d