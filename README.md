# Ethereum ETL Airflow

Read this article: https://cloud.google.com/blog/products/data-analytics/ethereum-bigquery-how-we-built-dataset

## Setting up Airflow DAGs using Google Cloud Composer

### Create BigQuery Datasets

- Sign in to BigQuery https://bigquery.cloud.google.com/
- Create new datasets called `crypto_ethereum`, `crypto_ethereum_raw`, `crypto_ethereum_temp`

### Create Google Cloud Storage bucket

- Create a new Google Storage bucket to store exported files https://console.cloud.google.com/storage/browser

### Create Google Cloud Composer environment

Create a new Cloud Composer environment:

```bash
export ENVIRONMENT_NAME=ethereum-etl-0
gcloud composer environments create $ENVIRONMENT_NAME --location=us-central1 --zone=us-central1-a \
    --disk-size=100GB --machine-type=n1-standard-4 --node-count=3 --python-version=3 --image-version=composer-1.8.3-airflow-1.10.3 \
    --network=default --subnetwork=default

gcloud composer environments update $ENVIRONMENT_NAME --location=us-central1 --update-pypi-package=ethereum-etl==1.7.0
```

Create variables in Airflow (**Admin > Variables** in the UI):

| Variable                                | Description                             |
|-----------------------------------------|-----------------------------------------|
| ethereum_output_bucket                  | GCS bucket to store exported files      |
| ethereum_provider_uris                  | Comma separated URIs of Ethereum nodes  |
| ethereum_destination_dataset_project_id | Project ID of BigQuery datasets         |
| notification_emails                     | email for notifications                 |

Check other variables in `dags/ethereumetl_airflow/variables.py`.

### Upload DAGs

```bash
> ./upload_dags.sh <airflow_bucket>
```

### Running Tests

```bash
pip install -r requirements.txt
export PYTHONPATH='dags'
pytest -vv -s
```

### Creating Table Definition Files for Parsing Events and Function Calls

Read this article: https://medium.com/@medvedev1088/query-ens-and-0x-events-with-sql-in-google-bigquery-4d197206e644
