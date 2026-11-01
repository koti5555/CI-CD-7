# Practical 7 — Automate Docker Build and Test Using GitHub Actions

This repository implements Practical 7 for the Student Result Prediction application.

## Objective

Automatically train and validate the ML model, build the Docker image, start the container, check the Flask health endpoint, test PASS and FAIL predictions, capture logs, and clean up the container on a GitHub-hosted runner.

## Repository structure

- `.github/workflows/ml-ci.yml` — complete ML and Docker CI workflow.
- `train_model.py` — generates the dataset and trains the model.
- `quality_gate.py` — enforces minimum accuracy of 0.85.
- `test_ml_pipeline.py` — seven ML pipeline tests.
- `app.py` — Flask `/health` and `/predict` endpoints.
- `test_app.py` — Flask application tests.
- `requirements.txt` — Python dependencies.
- `Dockerfile` and `.dockerignore` — container configuration.

## Workflow stages

1. Checkout source code.
2. Set up Python 3.11 and install dependencies.
3. Train the model and run the quality gate.
4. Run automated unit tests.
5. Build a Docker image tagged with the Git commit SHA.
6. Start the container on port 5000.
7. Retry `GET /health` until the application is ready.
8. Send PASS and FAIL requests to `POST /predict`.
9. Always collect container logs and remove the container.

## Trigger

The workflow runs on pushes to `main`, pull requests targeting `main`, and manual `workflow_dispatch`.

## Run without installing Docker locally

Open the **Actions** tab in this repository and select **Student Result ML CI-CD**. GitHub-hosted Ubuntu runners execute Docker build and container tests; Docker Desktop is not required on the student's computer.

## Expected prediction requests

PASS request:

```json
{"attendance":90,"internal_marks":85,"assignment_marks":88,"previous_score":80}
```

Expected prediction: `PASS`.

FAIL request:

```json
{"attendance":55,"internal_marks":30,"assignment_marks":40,"previous_score":35}
```

Expected prediction: `FAIL`.

## Controlled failure demonstration

Temporarily change the health-check URL in the workflow from `http://localhost:5000/health` to `http://localhost:5001/health`, commit the change, and verify that the health-check step fails while the log and cleanup steps still run. Restore port 5000 and commit again. The final corrected workflow should pass.

## Scope

This practical validates the Docker image inside a temporary GitHub Actions runner. It does not push the image to a container registry and does not deploy to production.
