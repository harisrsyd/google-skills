## [GSP528] Connecting Cloud Networks with NCC: Challenge Lab

### `🔗 Lab Link` - [*Click Here*](https://www.skills.google/course_templates/1364/labs/616120)

Enable service
```bash
gcloud services enable networkconnectivity.googleapis.com
```

## Task 1. Connect 2 On-prem VPCs with NCC
setup variable
```bash
PROJECT_ID=$(gcloud config get-value project)
REGION=$(gcloud compute vpn-tunnels list --format="value(region)" --limit=1)
```
1. Establish a Hub-and-Spoke architecture where the central hub connects to two spokes
```bash
# Create NCC Hub
gcloud network-connectivity hubs create ncc-hub \
    --project=$PROJECT_ID \
    --description="Central NCC Hub for On-Prem Offices"

# Gather VPN tunnels for On-Prem Offices
gcloud compute vpn-tunnels list --filter="name~'routing-to-onprem'"

# Save URI to variabel
export ROUTING_OFFICE1_T1=$(gcloud compute vpn-tunnels describe routing-to-onprem-office1-tunnel-0 --region=${REGION} --format="value(selfLink)")
export ROUTING_OFFICE1_T2=$(gcloud compute vpn-tunnels describe routing-to-onprem-office1-tunnel-1 --region=${REGION} --format="value(selfLink)")

export ROUTING_OFFICE2_T1=$(gcloud compute vpn-tunnels describe routing-to-onprem-office2-tunnel-0 --region=${REGION} --format="value(selfLink)")
export ROUTING_OFFICE2_T2=$(gcloud compute vpn-tunnels describe routing-to-onprem-office2-tunnel-1 --region=${REGION} --format="value(selfLink)")
```
2. Create Spoke for office 1
```bash
gcloud network-connectivity spokes linked-vpn-tunnels create spoke-office-1 \
    --project=${PROJECT_ID} \
    --hub=ncc-hub \
    --region=${REGION} \
    --vpn-tunnels="${ROUTING_OFFICE1_T1},${ROUTING_OFFICE1_T2}"
```
3. Create Spoke for office 2
```bash
gcloud network-connectivity spokes linked-vpn-tunnels create spoke-office-2 \
    --project=${PROJECT_ID} \
    --hub=ncc-hub \
    --region=${REGION} \
    --vpn-tunnels="${ROUTING_OFFICE2_T1},${ROUTING_OFFICE2_T2}"
```

## Task 2. Connect VPC to VPC
1. 
```bash
# 1. Check network for workload VPC
gcloud compute networks list --filter="name~'workload'"

# 2. Save URI VPC 1 dan VPC 2 into variabel
export WORKLOAD_VPC1=$(gcloud compute networks describe workload-vpc-1 --format="value(selfLink)")
export WORKLOAD_VPC2=$(gcloud compute networks describe workload-vpc-2 --format="value(selfLink)")
```
2. Create workload VPC spokes
```bash
gcloud network-connectivity spokes linked-vpc-network create spoke-workload-1 \
    --hub=ncc-hub \
    --global \
    --vpc-network="${WORKLOAD_VPC1}" \
    --description="Spoke for Workload VPC 1"


gcloud network-connectivity spokes linked-vpc-network create spoke-workload-2 \
    --hub=ncc-hub \
    --global \
    --vpc-network="${WORKLOAD_VPC2}" \
    --description="Spoke for Workload VPC 2"
```
3. Verify hub and spokes status (optional)
```bash
gcloud network-connectivity spokes list --global

gcloud network-connectivity hubs describe ncc-hub
```

## Task 3. Connect VPC to On-prem
1. Setup variable for VPC On-Prem Office 1
```bash
# Check VPC network list 
gcloud compute networks list --filter="name~'on-prem'"

# Save URI VPC into variabel
export ONPREM_VPC_URI=$(gcloud compute networks describe on-prem-office-1-vpc --format="value(selfLink)")
```
2. Create hybrid spokes for On-Prem Office 1
```bash
gcloud network-connectivity spokes linked-vpc-network create hybrid-office-1-spoke \
    --hub=ncc-hub \
    --global \
    --vpc-network="${ONPREM_VPC_URI}" \
    --description="Hybrid Spoke for On-Prem Office 1 VPC"
```
3. Verify hub and spokes status (optional)
```bash
gcloud network-connectivity spokes list --global

gcloud network-connectivity hubs describe ncc-hub
```