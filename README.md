# k8s-elk-lite
Kubernetes Elasticsearch deployment to AWS

The purpose of this project is to provide starter files for deploying a high performance Elasticsearch cluster on Kubernetes running on AWS. 

### Pre-requsites
+ Docker is installed
+ Kubernetes is installed with:
  + kops
  + kubectl

## K8S-AWS Cluster
### Topology
  + 3 K8S Master Nodes
  + 5 K8S Data Nodes:
    + 3 ELK Master Nodes
    + 2 ELK Data Nodes
    
### Step 1 - Setup KOPS environment variable to AWS S3 bucket and cluster AWS Availability Zone:
```
> export NAME=myk8snamespace
> export KOPS_STATE_STORE=s3://k8s-aws-bucket
> aws ec2 describe-availability-zones --region us-east-2
```
### Step 2 - Create K8S cluster 
Use KOPS for the following K8S HA: (5 K8S nodes & 3 K8S masters in multiple AZs).
```
> kops create cluster ${NAME} \
    --master-count=3 \
    --master-zones us-east-2a,us-east-2b,us-east-2c \
    --master-size=c4.large \
    --node-count=5 \
    --zones=us-east-2a,us-east-2b,us-east-2c \
    --node-size=t2.xlarge \
    --yes
```
### Step 3 - Validate cluster
All instances created by KOPS are build with ASG (Auto Scaling Groups) - ASG instances are automatically rebuilt and monitored if they suffer a failure.
```
> kops validate cluster
```
### Step 4 - Explore K8S Cluster
List nodes & pods (should be empty as no Docker containers have been deployed).
```
> kubectl get all
> kubectl get nodes --show-labels
> kubectl get pods
```
### Step 5 - Delete cluster - CAUTION
```
> kops delete cluster ${NAME} --yes
```

## K8S-ELK Cluster on AWS
CAUTION - Before executing any of the next steps make sure you are at the root directory called k8s-elk-lite, which contains the following sub-directories:
- 1_k8s-global
- 2_elasticsearch
- 3_kibana
- 4_beats_init
- 5_beats_agents
- 6_logstash

### Step 1 - 

### Step 2 - 

### Step 3 - 

### Step 4 - 

## Next Steps
