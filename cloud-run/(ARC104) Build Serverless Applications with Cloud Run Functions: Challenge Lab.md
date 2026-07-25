### **Task 1. Create a Cloud Storage bucket**

1. Setup your Cloud Console, activate Cloud Shell
2. Execute the following command to enable all the necessary services:

```bash
gcloud services enable \
  artifactregistry.googleapis.com \
  cloudfunctions.googleapis.com \
  cloudbuild.googleapis.com \
  eventarc.googleapis.com \
  run.googleapis.com \
  logging.googleapis.com \
  pubsub.googleapis.com \
  cloudaicompanion.googleapis.com
```

3. Click **Open Editor** on the Cloud Shell toolbar. 
4. Click **Cloud Code - No Project** in the status bar at the bottom of the screen.
5. Authorize the plugin if necessary. If a project is not automatically selected, click **Select a Google Cloud Project**, and choose your `Project ID`.
6. Configure with the following code to grant the `pubsub.publisher` IAM role to the Cloud Storage service account:

```bash
PROJECT_NUMBER=$(gcloud projects list --filter="project_id:$DEVSHELL_PROJECT_ID" --format='value(project_number)')
SERVICE_ACCOUNT=$(gsutil kms serviceaccount -p $PROJECT_NUMBER)

gcloud projects add-iam-policy-binding $DEVSHELL_PROJECT_ID \
  --member serviceAccount:$SERVICE_ACCOUNT \
  --role roles/pubsub.publisher
```

7. Set variable region and set another variable

```bash
export REGION="$YOUR_REGION"
export FUNCTION_NAME="$YOUR_FUNCTION_NAME"
export HTTP_FUNCTION="$YOUR_HTTP_FUNCTION"
```

8. Back to Terminal, run following command to create a Cloud Storage Bucket:

```bash
BUCKET="gs://$DEVSHELL_PROJECT_ID" 
gsutil mb -l $REGION $BUCKET
```

### **Task 2. Create, deploy, and test a Cloud Storage function**

1. In Cloud Shell, run the following command to set the default region:

```bash
gcloud config set run/region $REGION
```

2. Create the folder and files for the app, and navigate to the folder:

```bash
mkdir ~/$FUNCTION_NAME && cd $_
touch index.js && touch package.json
```

3. Use the following code blocks for the index.js and package.json:

index.js (replace `eventStorage` with your function name):

```jsx
const functions = require('@google-cloud/functions-framework');

functions.cloudEvent('~~eventStorage~~', (cloudevent) => {
  console.log('A new event in your Cloud Storage bucket has been logged!');
  console.log(cloudevent);
});
```

package.json:

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

4. Deploy the function by running the following command

```bash
gcloud functions deploy $FUNCTION_NAME \
  --gen2 \
  --runtime nodejs20 \
  --entry-point $FUNCTION_NAME \
  --source . \
  --region $REGION \
  --trigger-bucket $BUCKET \
  --trigger-location $REGION \
  --max-instances 2 \
  --quiet
  
```

5. Test the function by uploading any file to the bucket.

```bash
echo "Hello World" > random.txt
gsutil cp random.txt $BUCKET/random.txt
```

6. Run the following command. You should see the received CloudEvent in the logs:

```bash
gcloud functions logs read $FUNCTION_NAME \
  --region Region --gen2 --limit=100 --format "value(log)"
```

### **Task 3. Create and deploy a HTTP function with minimum instances**

1. Create a HTTP function that responds to HTTP requests. The function is written in **Node.js 20**

```bash
mkdir ~/$HTTP_FUNCTION && cd $_
touch index.js && touch package.json
```

index.js

```bash
const functions = require('@google-cloud/functions-framework');

functions.http('~~helloWorld~~', (req, res) => {
  res.status(200).send('HTTP function (2nd gen) has been called!');
});

```

package.json

```bash
{
  "name": "nodejs-functions-gen2-codelab",
  "version": "0.0.1",
  "main": "index.js",
  "dependencies": {
    "@google-cloud/functions-framework": "^2.0.0"
  }
}
```

2. Deploy the function by running the following command:

```bash
gcloud functions deploy $HTTP_FUNCTION \
  --gen2 \
  --runtime nodejs20 \
  --entry-point $HTTP_FUNCTION \
  --source . \
  --region $REGION \
  --trigger-http \
  --timeout 600s \
  --max-instances 2 \
  --min-instances 1 \
  --quiet
```
