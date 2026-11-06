# VerifiHire — Fake Job Posting Detection

A responsive premium-style web app for **ML-assisted job posting screening**, built with Flask, TF-IDF, and Logistic Regression.

## Features

- Responsive landing page and analysis dashboard
- Flask JSON API (`/api/analyze`) and health endpoint (`/api/health`)
- TF-IDF word and phrase features with a Logistic Regression classifier
- Risk score based on the model's predicted probability for class `1`
- Transparent heuristic warning indicators
- Input validation and request size limit
- Render deployment files included

## Project structure

```text
├── app.py                  # Flask web server and prediction API
├── train_model.py          # ML model training and evaluation script
├── requirements.txt        # Python package dependencies
├── Procfile                # WSGI deployment configuration (Gunicorn)
├── render.yaml             # Render cloud deployment blueprint
├── .python-version         # Python version specification
├── data/
│   └── sample_jobs.csv     # Labeled dataset for model training
├── models/
│   └── job_risk_model.joblib # Trained TF-IDF + Logistic Regression model
├── templates/
│   └── index.html          # Interactive frontend user interface
└── static/
    ├── css/style.css       # Premium modern UI stylesheet
    └── js/app.js           # Client-side validation and API communication
```

## Run locally (Windows)

1. Install Python 3.11.
2. Extract this folder and open a terminal inside it.
3. Create and activate a virtual environment:

```powershell
py -3.11 -m venv .venv
.venv\Scripts\activate
```

4. Install dependencies:

```powershell
python -m pip install --upgrade pip
pip install -r requirements.txt
```

5. Train the demo model:

```powershell
python train_model.py
```

6. Start the website:

```powershell
python app.py
```

7. Open `http://127.0.0.1:5000`.

### Important about the included sample data

`data/sample_jobs.csv` contains **synthetic examples only** so you can test the complete pipeline locally. It is deliberately small and is **not suitable for real-world decisions or performance claims**. The model trained on it is a functionality demo, not a reliable fraud detector.

For a serious project, use a reputable labeled dataset such as the Kaggle *Real or Fake Job Posting Prediction* dataset. Put the CSV in `data/` and run:

```powershell
python train_model.py data/your_dataset.csv
```

The CSV must include a `fraudulent` column (0 = legitimate, 1 = fraudulent) and at least one text field. The training script recognizes these text fields if present: `title`, `company_profile`, `description`, `requirements`, `benefits`, `employment_type`, and `location`.

The model's test metrics are printed during training. Use a large, representative dataset and evaluate precision, recall, F1-score, and the confusion matrix. Do not report the synthetic dataset metrics as real performance.

## API

### `GET /api/health`

Returns whether a model file has been loaded.

### `POST /api/analyze`

Content-Type: `application/json`

Example:

```json
{
  "title": "Junior Data Analyst",
  "company_profile": "Example company",
  "description": "Analyze data, write SQL queries, collaborate with the team.",
  "requirements": "Python and SQL",
  "benefits": "Health insurance"
}
```

Returns a model prediction, model confidence, model risk score, warning indicators, and a disclaimer. The `risk_score` is the model's probability estimate for class 1; it is not a verified real-world scam probability.

## Deploy to Render

1. Push this folder to a GitHub repository.
2. In Render, create a **New Web Service** and connect the repository.
3. Use Python runtime. Build command: `pip install -r requirements.txt`; start command: `gunicorn app:app`.
4. The model artifact is not committed by default. Configure a build step to train the model from a suitable dataset, or use a durable artifact-storage workflow and load the artifact at runtime. Do not train on the tiny synthetic sample for production use.
5. Verify `/api/health` and the home page after deployment.

## Limitations and safety

- Model predictions can be wrong and can reflect biases in training data.
- Keyword indicators are transparent heuristics, not proof of fraud.
- Never treat a low score as a guarantee that a job is genuine.
- This starter does not store submissions in a database and does not include accounts, authentication, or persistent scan history.
- For production, add monitoring, rate limiting, robust dataset/version management, tests, and a clear privacy policy.
