# Workflows Samples

[Workflows](https://cloud.google.com/workflows) allows you to orchestrate and automate Google Cloud and HTTP-based API services with serverless workflows.

In this sample, you will orchestrate multiple Cloud Functions, Cloud Run and
external services in a workflow.

## Service account for Workflows

Create a service account for Workflows:

```sh
export SERVICE_ACCOUNT=workflow-sa
gcloud iam service-accounts create ${SERVICE_ACCOUNT}
```

Grant permissions to the service account:

```sh
export PROJECT_ID=$(gcloud config get-value project)
gcloud projects add-iam-policy-binding ${PROJECT_ID} \
    --member "serviceAccount:${SERVICE_ACCOUNT}@${PROJECT_ID}.iam.gserviceaccount.com" \
    --role "roles/workflows.editor"
```

## Cloud Function - Random number

Inside [randomgen](randomgen) folder, deploy a function that generates a random number:

```sh
gcloud functions deploy randomgen --runtime python37 --trigger-http
--allow-unauthenticated
```

Test:

```sh
curl https://us-central1-workflows-atamel.cloudfunctions.net/randomgen
```

## Cloud Function - Multiply

Inside [multiply](multiply) folder, deploy a function that multiplies a given number:

```sh
gcloud functions deploy multiply --runtime python37 --trigger-http
--allow-unauthenticated
```

Test:

```sh
curl -X POST https://us-central1-workflows-atamel.cloudfunctions.net/multiply -H "content-type: application/json" -d '{"input":5}'
```

## External Function - MathJS

For an external function, use [MathJS](https://api.mathjs.org/).

Test:

```sh
curl https://api.mathjs.org/v4/?expr=2/3&precision=3
```

## Cloud Run - Floor

Inside [floor](floor) folder, deploy a Cloud Run service that floors a number.

Build the container:

```sh
export SERVICE_NAME=floor
gcloud builds submit --tag gcr.io/${PROJECT_ID}/${SERVICE_NAME}
```

Deploy:

```sh
gcloud run deploy ${SERVICE_NAME} \
  --image gcr.io/${PROJECT_ID}/${${SERVICE_NAME}} \
  --platform managed \
  --allow-unauthenticated
```

## Workflow

Deploy workflow:

```sh
gcloud beta workflows deploy workflow --source=workflow.yaml \
--service-account=${SERVICE_ACCOUNT}@${PROJECT_ID}.iam.gserviceaccount.com
```

Execute workflow:

```sh
gcloud beta workflows execute workflow
```

-------

This is not an official Google product.
