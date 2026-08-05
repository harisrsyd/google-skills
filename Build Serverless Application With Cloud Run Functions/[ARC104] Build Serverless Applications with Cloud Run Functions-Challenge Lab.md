# [ARC104] Build Serverless Applications with Cloud Run Functions: Challenge Lab

### `🔗 Lab Link` - [*Click Here*](https://www.skills.google/course_templates/696/labs/629778)

Enable API first
```bash
gcloud services enable \
  artifactregistry.googleapis.com \
  cloudfunctions.googleapis.com \
  cloudbuild.googleapis.com \
  eventarc.googleapis.com \
  run.googleapis.com \
  logging.googleapis.com \
  pubsub.googleapis.com
```

Store values in variable
```bash
# change .... with the right value
export PROJECT_ID=$DEVSHELL_PROJECT_ID
export REGION="...."
export BUCKET="gs://$DEVSHELL_PROJECT_ID"
export CS_FUNCTION="...."
export HTTP_FUNCTION="...."
```

## Task 1. Create a Cloud Storage Bucket
- Create Storage Bucket in your REGION and name it with your PROJECT_ID
```bash
gcloud config set run/region $REGION
gsutil mb -l $REGION $BUCKET
```

## Task 2. Create Deploy and test Cloud Storage Function
```bash
# Setup service account publisher IAM role
PROJECT_NUMBER=$(gcloud projects list --filter="project_id:$PROJECT_ID" --format='value(project_number)')
SERVICE_ACCOUNT=$(gsutil kms serviceaccount -p $PROJECT_NUMBER)

# run this after service_account value created
gcloud projects add-iam-policy-binding $PROJECT_ID \
  --member serviceAccount:$SERVICE_ACCOUNT \
  --role roles/pubsub.publisher
```
- create source function folder
```bash
mkdir ~/$CS_FUNCTION && cd $_
touch index.js && touch package.json
```
- edit index.js and package.json. The function is written in Node.js 24.
```jsx
// index.js (change .... with your function name)
const functions = require('@google-cloud/functions-framework');

functions.cloudEvent('....', (cloudevent) => {
  console.log('A new event in your Cloud Storage bucket has been logged!');
  console.log(cloudevent);
});
```
```json
// package.json
{
  "name": "nodejs-functions-gen2-codelab",
  "version": "0.0.1",
  "main": "index.js",
  "dependencies": {
    "@google-cloud/functions-framework": "^2.0.0"
  }
}
```
- Deploy with maximum 2 instances
```bash
gcloud functions deploy $CS_FUNCTION \
  --gen2 \
  --runtime nodejs24 \
  --entry-point $CS_FUNCTION \
  --source . \
  --region $REGION \
  --trigger-bucket $BUCKET \
  --trigger-location $REGION \
  --max-instances 2
```
```bash
# test
echo "Hello World" > random.txt
gsutil cp random.txt $BUCKET/random.txt

# test view log
gcloud functions logs read $CS_FUNCTION \
  --region $REGION --gen2 --limit=100 --format "value(log)"
```

### Task 3
#### Create and deploy a HTTP function
- Create source code
```bash
mkdir ~/$HTTP_FUNCTION && cd $_
touch index.js && touch package.json
```
- edit index.js and package.json
```js
const functions = require('@google-cloud/functions-framework');

functions.http('....', (req, res) => {
  res.status(200).send('HTTP function (2nd gen) has been called!');
});
```
```json
{
  "name": "nodejs-functions-gen2-codelab",
  "version": "0.0.1",
  "main": "index.js",
  "dependencies": {
    "@google-cloud/functions-framework": "^2.0.0"
  }
}
```
- Deploy with minimum 1 and maximum 2 instances
```bash
gcloud functions deploy $HTTP_FUNCTION \
  --gen2 \
  --runtime nodejs24 \
  --entry-point $HTTP_FUNCTION \
  --source . \
  --region $REGION \
  --trigger-http \
  --timeout 600s \
  --max-instances 2 \
  --min-instances 1
```
```bash
```

## Congratulations!! 🎉🎉 