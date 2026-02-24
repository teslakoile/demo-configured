---
name: model-serving
description: Use when building ML model training scripts or prediction endpoints. Provides patterns for model training with scikit-learn, model persistence, and serving with proper input validation.
---
# Model Serving

## Training Script (`src/train.py`)

1. Load a scikit-learn built-in dataset (e.g., `load_iris()`).
2. Split with `train_test_split(test_size=0.2, random_state=42)`.
3. Train a `RandomForestClassifier(n_estimators=100, random_state=42)`.
4. Evaluate: log accuracy score and classification report (structured logs; no `print()` in `src/`).
5. Save with `joblib.dump()` to `models/{model_type}_{dataset}_{YYYYMMDD}.joblib`.
6. Log the saved model path (structured log output).

## Pydantic Schemas (`src/schemas.py`)

Define explicit schemas:

```python
class PredictionRequest(BaseModel):
    sepal_length: float = Field(..., gt=0, description="Sepal length in cm")
    sepal_width: float = Field(..., gt=0, description="Sepal width in cm")
    petal_length: float = Field(..., gt=0, description="Petal length in cm")
    petal_width: float = Field(..., gt=0, description="Petal width in cm")
```

Response schema must include prediction label, confidence score, and the full envelope wrapper.

## Model Loading

- Load model once at startup via lifespan context manager.
- Find the latest `.joblib` file in `models/` by filename date.
- Store in `app.state.model` and `app.state.model_version`.
- If no model file exists, log a warning and set `app.state.model = None`. The health endpoint should report `model_loaded: false`.

## Prediction Endpoint

1. Validate input via Pydantic schema.
2. Convert to numpy array in the correct feature order.
3. Call `model.predict()` and `model.predict_proba()`.
4. Measure inference time in milliseconds.
5. Return the standard response envelope with prediction, confidence, and meta.
