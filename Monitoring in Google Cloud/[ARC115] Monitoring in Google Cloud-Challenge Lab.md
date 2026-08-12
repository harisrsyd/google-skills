# [ARC115] Monitoring in Google Cloud: Challenge Lab

### `🔗 Lab Link` - [*Click Here*]()

## Task 1. Install the Cloud Logging and Monitoring agents using Ops Agent
1. In the Cloud Console, select **Navigation Menu > Compute Engine > VM instance**.
2. Connect to VM instance via SSH by clicking **SSH** (see the **VM instance name** and **zone**, will be used later)
3. Update package list
```bash
sudo apt-get update
```
4. install the Ops Agent
```bash
curl -sSO https://dl.google.com/cloudagents/add-google-cloud-ops-agent-repo.sh
sudo bash add-google-cloud-ops-agent-repo.sh --also-install
```
5. modify the agent's core configuration file and restart Ops Agent
```bash
# Configures Ops Agent to collect telemetry from the app and restart Ops Agent.
set -e

# Create a back up of the existing file so existing configurations are not lost.
sudo cp /etc/google-cloud-ops-agent/config.yaml /etc/google-cloud-ops-agent/config.yaml.bak

# Configure the Ops Agent.
sudo tee /etc/google-cloud-ops-agent/config.yaml > /dev/null << EOF
metrics:
  receivers:
    apache:
      type: apache
  service:
    pipelines:
      apache:
        receivers:
          - apache
logging:
  receivers:
    apache_access:
      type: apache_access
    apache_error:
      type: apache_error
  service:
    pipelines:
      apache:
        receivers:
          - apache_access
          - apache_error
EOF

sudo service google-cloud-ops-agent restart
sleep 60
```
6. Verify agent
```bash
# verify agent, must running (pres 'q' for exit)
sudo systemctl status google-cloud-ops-agent"*"
```

## Task 2. Add an uptime check for Apache Web Server on the VM
- In cluod shell, run this:
```bash
# export environment variable
export VM_NAME=....
export ZONE=....
gcloud config set compute/zone $ZONE
export VM_IP=$(gcloud compute instances describe $VM_NAME \
    --format="value(networkInterfaces[0].accessConfigs[0].natIP)")

# create uptime check
gcloud monitoring uptime create $VM_NAME-uptime \
    --resource-type=uptime-url \
    --resource-labels=host=${VM_IP} \
    --protocol=http \
    --port=80 \
    --path="/"
```

## Task 3. Add an alert policy for Apache Web Server
1. Create email notification channel
```bash
# change with your email or student email
export USER_EMAIL="your-email@example.com"

# Create the notification channel and retrieve its ID
CHANNEL_ID=$(gcloud alpha monitoring channels create \
    --display-name="My Email" \
    --type=email \
    --channel-labels=email_address=${USER_EMAIL} \
    --format="value(name)")

echo "Created Notification Channel ID: ${CHANNEL_ID}"
```
2. Create the Alert Policy for Traffic Rate (> 3 KiB/s)
```bash
# Create Alert Policy JSON definition file
cat <<EOF > apache_alert_policy.json
{
  "displayName": "Apache Web Server Traffic Alert",
  "documentation": {
    "content": "Alert triggered when Apache traffic exceeds 3 KiB/s",
    "mimeType": "text/markdown"
  },
  "conditions": [
    {
      "displayName": "Apache Traffic Exceeds 3 KiB/s",
      "conditionThreshold": {
        "filter": "resource.type = \"gce_instance\" AND metric.type = \"workload.googleapis.com/apache.traffic\"",
        "aggregations": [
          {
            "alignmentPeriod": "60s",
            "perSeriesAligner": "ALIGN_RATE"
          }
        ],
        "comparison": "COMPARISON_GT",
        "thresholdValue": 3072,
        "duration": "0s",
        "trigger": {
          "count": 1
        }
      }
    }
  ],
  "alertStrategy": {
    "autoClose": "604800s"
  },
  "combiner": "OR",
  "enabled": true,
  "notificationChannels": [
    "${CHANNEL_ID}"
  ]
}
EOF

# Create the policy using gcloud
gcloud alpha monitoring policies create --policy-from-file="apache_alert_policy.json"
```
3. Generate traffic via SSH
- Connect to VM Instance via SSH or access it with command below:
```bash
gcloud compute ssh $VM_NAME --zone=$ZONE
```
- Run the traffic generation command
```bash
timeout 120 bash -c -- 'while true; do curl localhost | grep -oP "<title>.*</title>"; sleep .1s;done '
```

## Task 4. Create a dashboard and charts for Apache Web Server on the VM
1. In cloud shell, create the dashboard definition JSON file
```bash
cat <<EOF > apache_dashboard.json
{
  "displayName": "Apache Web Server Dashboard",
  "gridLayout": {
    "columns": "2",
    "widgets": [
      {
        "title": "CPU Load",
        "xyChart": {
          "dataSets": [
            {
              "timeSeriesQuery": {
                "timeSeriesFilter": {
                  "filter": "resource.type = \"gce_instance\" AND metric.type = \"agent.googleapis.com/cpu/load_1m\"",
                  "aggregation": {
                    "alignmentPeriod": "60s",
                    "perSeriesAligner": "ALIGN_MEAN"
                  }
                }
              },
              "plotType": "LINE"
            }
          ]
        }
      },
      {
        "title": "Apache Requests",
        "xyChart": {
          "dataSets": [
            {
              "timeSeriesQuery": {
                "timeSeriesFilter": {
                  "filter": "resource.type = \"gce_instance\" AND metric.type = \"workload.googleapis.com/apache.requests\"",
                  "aggregation": {
                    "alignmentPeriod": "60s",
                    "perSeriesAligner": "ALIGN_RATE"
                  }
                }
              },
              "plotType": "LINE"
            }
          ]
        }
      }
    ]
  }
}
EOF
```
2. Deploy the dashboard
```bash
gcloud monitoring dashboards create --config-from-file=apache_dashboard.json
```

## Task 5. Create a log-based metric
```bash
gcloud logging metrics create apache_access_200_logs \
    --description="Log metric for Apache 200 HTTP responses" \
    --log-filter='resource.type="gce_instance" AND logName="projects/'$(gcloud config get-value project)'/logs/apache-access" AND textPayload:"200"'
```
- Explore the log-based metrics by selecting the metric **VM Instance > Apache > Workload/apache.requests**.

## Congratulations!! 🎉🎉 