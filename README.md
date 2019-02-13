# k8s-elk-lite
Kubernetes Elasticsearch deployment to AWS

The purpose of this project is to provide starter files for deploying a high performance Elasticsearch cluster on Kubernetes running on AWS.

### Pre-requsites
+ Docker is installed
+ Kubernetes is installed with:
  + kops
  + kubectl
+ AWS SDK with proper keys

## K8S-AWS Cluster
### Topology
  + 3 K8S Master Nodes
  + 5 K8S Data Nodes:
    + 3 ELK Master Nodes
    + 2 ELK Data Nodes

### Step 1 - Setup KOPS environment variable to AWS S3 bucket and cluster AWS Availability Zone:
```
> export NAME=k8s-elk-lite-aws.k8s.local
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
- /k8s-elk-lite
  - /1_k8s-global
  - /2_elasticsearch
  - /3_kibana
  - /4_beats_init
  - /5_beats_agents
  - /6_logstash

### Step 1 - Set Root directory
Go to root directory of main templates
```
> cd /github/k8s-elk-lite
```

### Step 2 - Create K8S Namespace
Apply the namespace file in the 1_k8s_global directory and verify its creation.
```
> kubectl apply -f 1_k8s-global/namespace.yml
> kubectl get namespaces
```

### Step 3 - Create AWS storage
Create the AWS storage and verify its creation.
```
> kubectl apply -f 1_k8s-global/aws-storage.yml
> kubectl describe sc --namespace=k8s-elk-lite
```

### Step 4 - Create Elasticsearch Cluster
Create 3 ELK Master Nodes and 2 ELK Data Nodes.  Verify its creation.
```
> kubectl apply -f 2_elasticsearch
> kubectl get all --namespace=k8s-elk-lite
> kubectl rollout status sts/elasticsearch-data --namespace=k8s-elk-lite
> kubectl describe pod/elasticsearch-data-0 --namespace=k8s-elk-lite
```

### Step 5 - Access Elasticsearch URL
Check that your Elasticsearch cluster is functioning correctly by performing a request against the REST API:
```
> kubectl port-forward elasticsearch-data-0 9200:9200 --namespace=k8s-elk-lite

In a separate terminal, run:
> curl http://localhost:9200/_cluster/state?pretty
> curl http://localhost:9200/_cluster/health?pretty
> curl http://localhost:9200/_cat/nodes?v
```

### Step 6 - Start Kibana
create a Kibana Service and Deployment and verify if pod and service are ready.
```
> kubectl apply -f 3_kibana
> kubectl get all --namespace=k8s-elk-lite

Forward the local port 5601 to port 5601 on this Pod:
> kubectl port-forward kibana-xxxxxxxxxxxxxxxxxx 5601:5601 --namespace=k8s-elk-lite
```
Then try browser: http://localhost:5601

### Step 7 - Configure Kibana with Beat
Set up Index Templates and Kibana dashboards
```
> kubectl apply -f 4_beats_init
> kubectl get jobs --namespace=k8s-elk-lite
```

### Step 8 - Collect Logs and Metrics
Start Filebeat and Metricbeat Daemons.  Verify documents.
```
> kubectl apply -f 5_beats_agents
> kubectl get all --namespace=k8s-elk-lite

Connect to Kibana by opening a tunnel:
> kubectl port-forward service/kibana 5601 --namespace=k8s-elk-lite
```
Then try browser: http://localhost:5601

### Step 9 - Start Logstash
Create a Service and Deployment for Logstash.  Verify its creation.
```
> kubectl apply -f 6_logstash
> kubectl get all --namespace=k8s-elk-lite
```

## Next Steps
