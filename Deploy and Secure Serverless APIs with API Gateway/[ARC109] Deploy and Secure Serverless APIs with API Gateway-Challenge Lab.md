# [ARC109] Deploy and Secure Serverless APIs with API Gateway: Challenge Lab

### `🔗 Lab Link` - [*Click Here*](https://www.skills.google/course_templates/662/labs/625109)

Setup environment variable
```bash
export PROJECT_ID=$DEVSHELL_PROJECT_ID
export REGION="...."
```

## Task 1. Create a Cloud Run function
- Create directory and http function source code
```bash
mkdir gcfunction && cd gcfunction

# file index.js for function Hello World
cat <<EOF > index.js
const functions = require('@google-cloud/functions-framework');

functions.http('helloHttp', (req, res) => {
  res.send('Hello World!');
});
EOF

# file package.json
cat <<EOF > package.json
{
  "name": "gcfunction",
  "version": "0.0.1",
  "dependencies": {
    "@google-cloud/functions-framework": "^3.0.0"
  }
}
EOF
```
- Deploy Cloud Run Function
```bash
gcloud functions deploy gcfunction \
    --gen2 \
    --region=$REGION \
    --runtime=nodejs22 \
    --source=. \
    --entry-point=helloHttp \
    --trigger-http \
    --allow-unauthenticated
```

## Task 2. Create an API Gateway
- Create `openapispec.yaml`
```bash
# Setup project number and get Compute Engine default service account
export PROJECT_NUMBER=$(gcloud projects describe $(gcloud config get-value project) --format="value(projectNumber)")

# Create the openapispec.yaml file
cd ~
cat <<EOF > openapispec.yaml
swagger: '2.0'
info:
  title: gcfunction API
  description: Sample API on API Gateway with a Google Cloud Run functions backend
  version: 1.0.0
schemes:
- https
produces:
- application/json
x-google-backend:
  address: https://gcfunction-$PROJECT_NUMBER.$REGION.run.app
paths:
  /gcfunction:
    get:
      summary: gcfunction
      operationId: gcfunction
      responses:
       '200':
          description: A successful response
          schema:
            type: string
EOF
```
- Create the API Resource
```bash
gcloud api-gateway apis create gcfunction-api \
    --display-name="gcfunction API" \
    --project=$(gcloud config get-value project)
```
- Create the API Config
```bash
export PROJECT_NUMBER=$(gcloud projects describe $(gcloud config get-value project) --format="value(projectNumber)")
export SERVICE_ACCOUNT="$PROJECT_NUMBER-compute@developer.gserviceaccount.com"

gcloud api-gateway api-configs create gcfunction-api \
    --api=gcfunction-api \
    --openapi-spec=openapispec.yaml \
    --backend-auth-service-account=$SERVICE_ACCOUNT \
    --display-name="gcfunction API" \
    --project=$(gcloud config get-value project)
```
- Deploy the Gateway
```bash
gcloud api-gateway gateways create gcfunction-api \
    --api=gcfunction-api \
    --api-config=gcfunction-api \
    --location=$REGION \
    --display-name="gcfunction API" \
    --project=$(gcloud config get-value project)
```

## Task 3. Create a Pub/Sub Topic and Publish Messages via API Backend
- Create the Pub/Sub Topic with Default Subscription
```bash
gcloud pubsub topics create demo-topic
```
- Update `package.json` and `index.js`
```bash
cd ~/gcfunction

# Update package.json
cat <<EOF > package.json
{
  "name": "gcfunction",
  "version": "0.0.1",
  "dependencies": {
    "@google-cloud/functions-framework": "^3.0.0",
    "@google-cloud/pubsub": "^3.4.1"
  }
}
EOF

# Update index.js
cat <<EOF > index.js
/**
 * Responds to any HTTP request.
 *
 * @param {!express:Request} req HTTP request context.
 * @param {!express:Response} res HTTP response context.
 */
const {PubSub} = require('@google-cloud/pubsub');
const pubsub = new PubSub();
const topic = pubsub.topic('demo-topic');
const functions = require('@google-cloud/functions-framework');

exports.helloHttp = functions.http('helloHttp', (req, res) => {

  // Send a message to the topic
  topic.publishMessage({data: Buffer.from('Hello from Cloud Run functions!')});
  res.status(200).send("Message sent to Topic demo-topic!");
});
EOF
```
- Redeploy the Cloud Run Function
```bash
gcloud functions deploy gcfunction \
    --gen2 \
    --region=$REGION \
    --runtime=nodejs22 \
    --source=. \
    --entry-point=helloHttp \
    --trigger-http \
    --allow-unauthenticated
```
- Invoke the Function via API Gateway
```bash
# Get the Gateway Host URL
export GATEWAY_URL=$(gcloud api-gateway gateways describe gcfunction-api --location=$REGION --format="value(defaultHostname)")

# Invoke the endpoint via API Gateway
curl -i "https://${GATEWAY_URL}/gcfunction"
```
<!-- - Verify the published message
```bash
gcloud pubsub subscriptions pull demo-topic-sub --auto-ack
``` -->

## Congratulations!! 🎉🎉 
