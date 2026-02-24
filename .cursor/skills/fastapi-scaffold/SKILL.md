---
name: fastapi-scaffold
description: Use when creating a new FastAPI application or adding endpoints. Provides the standard app factory pattern, lifespan management, middleware, and project layout for this project.
---
# FastAPI Scaffold

## App Factory Pattern

Create the FastAPI app in `src/main.py` using this structure:

1. Define a lifespan context manager that:
   - Loads the ML model at startup (store in `app.state.model`)
   - Logs startup info (model path, version)
   - Yields
   - Cleans up on shutdown

2. Create the FastAPI instance with:
   - `title`, `version`, `description` metadata
   - Lifespan context manager attached
   - CORS middleware (allow all origins for demo)

3. Register routes from `src/routes.py`

4. Add a global exception handler that catches unhandled exceptions and returns the standard error envelope with status 500.

## Required Endpoints

Every app must include:
- `GET /api/v1/health` returning the standard envelope with `data.model_loaded` and `data.service = "ok"` (use `meta.inference_time_ms = 0.0` for health checks)
- `GET /api/v1/model-info` returning model metadata (type, dataset, training date, feature names) in the standard envelope

## Uvicorn Entrypoint

Bottom of `src/main.py`:
```python
if __name__ == "__main__":
    import uvicorn
    uvicorn.run("src.main:app", host="0.0.0.0", port=8000, reload=True)
```

## Dependencies File
All dependencies are declared in `pyproject.toml`. No `requirements.txt`.
