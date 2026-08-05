# [ARC120] The Basics of Google Cloud Compute: Challenge Lab

### `🔗 Lab Link` - [*Click Here*](https://www.skills.google/course_templates/754/labs/597890)

Set default Region and Zone first
```bash
# change .... with the right value
gcloud config set compute/zone "...."
gcloud config set compute/region "...."
```

## Task 1. Create a Cloud Storage bucket
```bash
export ZONE=$(gcloud config get compute/zone)
export REGION=$(gcloud config get compute/region)
export BUCKET="gs://$DEVSHELL_PROJECT_ID-bucket"

gcloud storage buckets create $BUCKET --location=US
```

## Task 2. Create and attach a persistent disk to a Compute Engine instance
```bash
# create compute instance
gcloud compute instances create my-instance \
    --zone=$ZONE \
    --machine-type=e2-medium \
    --boot-disk-type=pd-balanced \
    --boot-disk-size=10GB \
    --image-family=debian-12 \
    --image-project=debian-cloud \
    --tags=http-server

# create persistence disk
gcloud compute disks create mydisk --size=200GB \
--zone $ZONE

# attach disk to compute instance
gcloud compute instances attach-disk my-instance --disk mydisk --zone $ZONE
```

## Task 3. Install a NGINX web server
open ssh terminal for my-instance
```bash
gcloud compute ssh my-instance --zone=$ZONE
```
in ssh terminal
```bash
# update os
sudo apt-get update

# install NGINX
sudo apt-get install -y nginx

# confirm NGINX running
ps auwx | grep nginx
```

## Congratulations!! 🎉🎉