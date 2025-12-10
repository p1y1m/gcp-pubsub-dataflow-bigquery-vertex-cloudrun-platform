
Author: Pedro Yáñez Meléndez

# GCP Data and ML Platform in Colab

This project shows how I design a GCP data and machine learning platform.
I use Google Colab and Python.
I simulate key GCP services with small classes and short code cells.

## Goals

- Show understanding of modern GCP architecture.
- Work with streaming data, batch data, and ML in one flow.
- Use many GCP services in one consistent design.
- Keep the code simple and easy to read in Colab.

## GCP Services Covered (simulated in code)

- **Pub/Sub** – Receive events from producers.
- **Dataflow** – Transform events and build clean rows.
- **BigQuery** – Store analytics tables.
- **Cloud Storage** – Store JSON summary files.
- **Dataproc** – Run batch analytics on summaries.
- **Vertex AI** – Train a logistic regression model.
- **Cloud Run** – Expose a prediction API.
- **Cloud Functions** – Validate and enrich requests before the API.
- **Cloud Scheduler** – Trigger daily jobs.
- **Cloud Composer (Airflow)** – Orchestrate the full pipeline as a DAG.

All of these components are implemented in Python inside Colab.
Each service has a small class in the `src` folder.
I call them step by step so I can see each part of the pipeline.

## Architecture Overview

- Events enter the system through **Pub/Sub**.
- **Dataflow** reads Pub/Sub messages and creates processed rows.
- **BigQuery** receives these rows in the table `analytics.events`.
- **Cloud Storage** receives a daily JSON summary of revenue.
- **Dataproc** reads the summary and computes extra metrics.
- **BigQuery** stores the Dataproc results in `analytics.daily_summary_dp`.
- **Vertex AI** trains a logistic regression model to predict high value events.
- **Cloud Run** uses the trained model to return a prediction score.
- **Cloud Functions** validates the request, adds flags and buckets, then calls Cloud Run.
- **Cloud Scheduler** triggers a daily pipeline job that runs Cloud Function and Dataproc.
- **Cloud Composer (Airflow)** defines tasks for ingest, train, batch, and predict in one DAG.

The whole flow stays inside Colab.
I simulate the behavior of GCP services, but I keep the GCP names and patterns.
This helps me practice real GCP design even without a GCP account.

## Repository Structure

- `src/pubsub_mock.py` – Pub/Sub simulation client.
- `src/dataflow_sim.py` – Dataflow job simulation.
- `src/bigquery_sim.py` – BigQuery client simulation.
- `src/cloud_storage_sim.py` – Cloud Storage simulation.
- `src/dataproc_sim.py` – Dataproc job simulation.
- `src/vertex_ai_sim.py` – Vertex AI client with logistic regression.
- `src/cloud_run_sim.py` – Cloud Run API simulation.
- `src/cloud_function_sim.py` – Cloud Function simulation.
- `src/cloud_scheduler_sim.py` – Cloud Scheduler simulation.
- `src/composer_sim.py` – Cloud Composer / Airflow DAG simulation.

Each file is small.
Each class has clear methods.
I run them from Colab cells in a natural order.

## Data and Model

- Input data is a list of events with `user_id`, `event`, and `price`.
- The pipeline marks each row as processed.
- BigQuery tables hold events and daily summaries.
- Cloud Storage holds a simple JSON summary of total revenue and number of events.
- Dataproc computes average revenue and writes it back to BigQuery.
- Vertex AI uses `price` as the feature and predicts if an event is high value.
- The model is a logistic regression from scikit-learn.

## API and Orchestration

- Cloud Run receives an HTTP-style payload with `user_id`, `event`, and `price`.
- Cloud Functions checks that `price` exists and is positive.
- Cloud Functions adds:
  - `high_value` flag.
  - `price_bucket` as low, medium, or high.
- Cloud Functions then calls Cloud Run to get a prediction score.
- Cloud Scheduler triggers a daily job that:
  - Sends a request through Cloud Functions and Cloud Run.
  - Runs Dataproc on the daily summary.
  - Updates the BigQuery summary table.
- Cloud Composer defines a DAG with four tasks:
  - `ingest` – Pub/Sub, Dataflow, and BigQuery.
  - `train` – Vertex AI using BigQuery data.
  - `batch` – Cloud Storage and Dataproc.
  - `predict` – Cloud Functions and Cloud Run.

## Skills Demonstrated

- Design an end-to-end GCP-style data and ML platform.
- Combine Pub/Sub, Dataflow, BigQuery, Cloud Storage, Dataproc, Vertex AI, Cloud Run, Cloud Functions, Cloud Scheduler, and Cloud Composer.
- Build clear Python code that simulates cloud behavior in Colab.
- Use logistic regression for a simple ML use case.
- Think in terms of streaming, batch, APIs, and orchestration.

## How to Use in Colab

- Clone or download the project into the Colab environment.
- Run the cells in order:
  - Create the folders and base files.
  - Build each service class in `src`.
  - Run the pipeline pieces step by step.
  - Run the Composer DAG simulation at the end.
- Observe the printed logs and the Python objects.
- Use this project as a base to extend with more features or real GCP calls in the future.

