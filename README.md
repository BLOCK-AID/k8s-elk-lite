# k8s-elk-lite
Kubernetes Elasticsearch deployment to AWS

The purpose of this project is to provide starter files for deploying a high performance Elasticsearch cluster on Kubernetes running on AWS. 

### Pre-requsites
+ Docker is installed
+ Kubernets is installed with:
  + kops
  + kubectl

## K8S-AWS Cluster
### Topology
  + 3 K8S Master Nodes
  + 5 K8S Data Nodes:
    + 3 ELK Master Nodes
    + 2 ELK Data Nodes
    
### Step 1 - Setup KOPS environment variable to AWS S3 bucket and cluster AWS Availability Zone:
> export NAME=blockaid-k8s-aws.k8s.local
> export KOPS_STATE_STORE=s3://blockaid-k8s-aws-bucket
> aws ec2 describe-availability-zones --region us-east-2

## K8S-ELK Cluster on AWS


## Next Steps
