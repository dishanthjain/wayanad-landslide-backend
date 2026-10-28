# 🌍 AI-Based Landslide Susceptibility Assessment of Wayanad, Kerala

### Backend Architecture & REST API

A machine learning-based backend system for assessing landslide susceptibility in Wayanad, Kerala, using geospatial and environmental data. The backend provides REST APIs that connect the trained machine learning model with the frontend dashboard.

## 📌 About the Project

Landslides are a major natural hazard in hilly regions such as Wayanad, Kerala. This project aims to assess which geographical areas may be more susceptible to landslides by analyzing environmental and terrain-related factors.

The backend is developed using **FastAPI** and provides endpoints for susceptibility prediction, scenario simulation, feature explanations, health monitoring, and dashboard statistics.

The system is designed to integrate with a Next.js frontend and a trained machine learning model.

## 🎯 Project Objectives

- Analyze terrain and environmental features associated with landslides.
- Provide susceptibility predictions through REST APIs.
- Validate incoming data using Pydantic.
- Support what-if simulations involving rainfall and vegetation changes.
- Provide model explanations using SHAP when supported by the trained model.
- Deliver structured JSON responses to the frontend.
- Support containerization using Docker.

## 👨‍💻 My Role

**Backend Architecture & REST API Engineer — Dishanth Jain**

My responsibilities include:

- Developing the backend server using FastAPI.
- Implementing five REST API endpoints.
- Loading the trained machine learning model during application startup.
- Validating API inputs using Pydantic schemas.
- Integrating model predictions and SHAP explanations.
- Implementing environmental scenario simulations.
- Configuring CORS for frontend integration.
- Preparing Docker and dependency configuration files.
- Testing the API through Swagger UI.

## 🛠️ Technology Stack

| Technology | Purpose |
|---|---|
| Python | Backend programming |
| FastAPI | REST API framework |
| Uvicorn | ASGI server |
| Pydantic | Request validation and response schemas |
| Scikit-learn | Machine learning model compatibility |
| Joblib | Loading the trained model |
| NumPy | Numerical operations |
| Pandas | Preparing feature data |
| SHAP | Explaining model predictions |
| Docker | Containerization |
| Swagger / OpenAPI | API documentation and testing |

## 🏗️ System Architecture

```text
Next.js Frontend / Streamlit
             |
             | HTTP Requests / JSON
             v
       FastAPI Backend
             |
      Pydantic Validation
             |
             v
      Trained ML Model
             |
       Prediction Output
             |
             v
       JSON Response
             |
             v
       Frontend Dashboard
```

The backend receives environmental features, validates the input, passes the data to the trained model, and returns a structured response to the frontend.

## 📂 Project Structure

```text
wayanad-landslide-project/
├── backend/
│   ├── __init__.py
│   ├── main.py
│   └── schemas.py
├── models/
│   └── landslide_model.joblib
├── requirements.txt
├── Dockerfile
├── .dockerignore
└── README.md
```

### File Descriptions

- `backend/main.py` — Contains the FastAPI application, endpoints, model loading, prediction logic, simulation, and explanation functionality.
- `backend/schemas.py` — Defines Pydantic request and response models.
- `backend/__init__.py` — Marks the backend directory as a Python package.
- `models/landslide_model.joblib` — Stores the trained model supplied by the machine learning team.
- `requirements.txt` — Lists the Python dependencies.
- `Dockerfile` — Defines how to build the backend container.
- `.dockerignore` — Excludes unnecessary files from the Docker build context.
- `README.md` — Documents the project and setup instructions.

## 🚀 Installation and Setup

### Prerequisites

Install the following before starting:

- Python 3.11 or a compatible Python version
- Visual Studio Code or another code editor
- Git
- Docker Desktop, if using Docker

### 1. Clone the Repository

Replace the placeholder URL with the actual GitHub repository URL.

```zsh
git clone YOUR_GITHUB_REPOSITORY_URL
cd wayanad-landslide-project
```

### 2. Create a Virtual Environment

```zsh
python3 -m venv .venv
source .venv/bin/activate
```

### 3. Install Dependencies

```zsh
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

### 4. Add the Trained Model

Obtain the compatible `landslide_model.joblib` file from the machine learning team and place it inside the `models` directory.

The model must support `predict_proba()` and accept the expected feature names, order, and preprocessing used by the backend.

**Note:** The trained model is not included in this repository by default. The backend can start without it, but model-dependent endpoints return HTTP 503 until the model is available.

### 5. Start the Backend

Run the following command from the project root:

```zsh
python -m uvicorn backend.main:app --reload --port 8000
```

The API will be available at:

`http://127.0.0.1:8000`

## 📡 REST API Endpoints

The backend provides five main endpoints.

### 1. Health Check

**Method:** `GET`

**Endpoint:** `/health`

Checks whether the API is running and whether the machine learning model loaded successfully.

Example response:

```json
{
  "status": "ok",
  "model_loaded": true,
  "model_type": "RandomForestClassifier",
  "timestamp": "2026-10-28T15:00:00+00:00",
  "active_cluster_health": "not_configured",
  "model_error": null
}
```

The values above are illustrative. The actual response depends on the loaded model and server state.

### 2. Landslide Susceptibility Prediction

**Method:** `POST`

**Endpoint:** `/predict`

Receives environmental and geographical features and returns a model-based susceptibility score.

Example request:

```json
{
  "slope": 38.5,
  "rainfall_mm": 280.0,
  "elevation": 950.0,
  "twi": 11.2,
  "ndvi": 0.35,
  "dist_stream_m": 45.0
}
```

Example response:

```json
{
  "susceptibility_score": 0.72,
  "risk_category": "HIGH",
  "evacuation_alert": false,
  "model_type": "RandomForestClassifier"
}
```

The response is illustrative and does not represent an actual prediction for Wayanad.

The susceptibility score is between 0 and 1. The backend currently uses these demonstration categories:

| Score | Category |
|---|---|
| Below 0.25 | LOW |
| 0.25 to below 0.50 | MODERATE |
| 0.50 to below 0.75 | HIGH |
| 0.75 to 1.00 | CRITICAL |

These thresholds must be validated for the trained model before any operational use.

### 3. Scenario Simulation

**Method:** `POST`

**Endpoint:** `/simulate`

Compares baseline susceptibility with a simulated scenario involving changes in rainfall and vegetation.

Example request:

```json
{
  "slope": 38.5,
  "rainfall_mm": 280.0,
  "elevation": 950.0,
  "twi": 11.2,
  "ndvi": 0.35,
  "dist_stream_m": 45.0,
  "rainfall_increase_pct": 40,
  "vegetation_loss_pct": 20
}
```

The backend calculates the baseline score, simulated score, score difference, and corresponding categories.

The result depends on the trained model and its learned relationships. It should not be assumed that every increase in rainfall or loss of vegetation will produce a particular numerical result.

### 4. Model Explainability

**Method:** `GET`

**Endpoint:** `/explain`

Accepts the six environmental features as query parameters and returns SHAP feature contributions when a compatible explainer is available.

Example request:

`/explain?slope=38.5&rainfall_mm=280&elevation=950&twi=11.2&ndvi=0.35&dist_stream_m=45`

The response includes feature names, contribution values, the model type, and the explanation method.

SHAP values describe contributions to the model output according to the selected explainer. They are not automatically percentages or proof of causation.

### 5. Dashboard Statistics

**Method:** `GET`

**Endpoint:** `/dashboard-stats`

Returns structured statistics for frontend dashboard cards and sector summaries.

The current implementation contains demonstration values for:

- Mean susceptibility
- Rainfall recorded over 24 hours
- Sensor node count
- Chooralmala
- Meppadi
- Vythiri
- Sulthan Bathery

**Important:** These values are static demonstration data, not verified live measurements. Real sensor integration and authoritative data sources must be added before presenting them as live statistics.

## 🧪 API Testing

FastAPI automatically generates interactive API documentation.

After starting the backend, open:

- **Swagger UI:** http://127.0.0.1:8000/docs
- **ReDoc:** http://127.0.0.1:8000/redoc
- **OpenAPI JSON:** http://127.0.0.1:8000/openapi.json
- **Health endpoint:** http://127.0.0.1:8000/health

Use Swagger UI to send requests to each endpoint and inspect the JSON responses.

### Input Validation

Pydantic validates incoming request data. For example:

- Slope must be between 0 and 90 degrees.
- Rainfall cannot be negative.
- NDVI must be between -1 and 1.
- Unexpected request fields are rejected.

Invalid request bodies or query parameters are automatically rejected with HTTP 422 responses.

## 🐳 Docker Setup

The project includes a Dockerfile for running the FastAPI server in a container.

### Build the Docker Image

Run this command from the project root:

```zsh
docker build -t wayanad-landslide-api .
```

### Run the Container

```zsh
docker run --rm -p 8000:8000 wayanad-landslide-api
```

Open http://127.0.0.1:8000/docs to access the API documentation.

Ensure that the trained model is available in the build context before building the final image.

## 🔐 CORS Configuration

The backend allows requests from these local development origins:

- `http://localhost:3000`
- `http://127.0.0.1:3000`
- `http://localhost:8501`
- `http://127.0.0.1:8501`

These correspond to the Next.js frontend and Streamlit application during local development.

Production deployments should configure only the required trusted frontend origins.

## ⚠️ Limitations and Safety

- Predictions depend on the quality, coverage, and preprocessing of the training data.
- The model file must be compatible with the backend's expected feature schema.
- Dashboard statistics are currently demonstration values.
- The active cluster health field is a placeholder; distributed cluster monitoring has not been implemented.
- Risk thresholds and evacuation alerts are demonstration logic, not official disaster-management warnings.
- Susceptibility assessment does not predict the exact time of a landslide.
- The system is an academic project and must not replace official warnings or professional disaster-management decisions.

## 🔮 Future Enhancements

- Integrate verified rainfall and environmental datasets.
- Connect live sensor telemetry when a reliable source is available.
- Add model versioning and stronger model validation.
- Improve SHAP support for the full preprocessing pipeline.
- Add automated API tests and continuous integration.
- Deploy the backend to a cloud hosting platform.
- Add authentication, request logging, monitoring, and production security controls.

## 👥 Team Collaboration

This project combines geospatial analysis, machine learning, backend API development, and frontend visualization.

The backend acts as the integration layer between the trained model and the user interface, making prediction results available through structured REST endpoints.

---

**Developed as part of an academic mini project:** AI-Based Landslide Susceptibility Assessment of Wayanad, Kerala Using Geospatial and Environmental Data.

**Backend contribution:** Dishanth Jain — Backend Architecture & REST API.
