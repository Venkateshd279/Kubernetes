# Kubernetes
This repo contains kubernetes related stuff. 

## PREREQUIREMENTS
1. linux machine (ubuntu)
2. AWS account
3. kops binary (kubernetes cluster initiate)
4. kubectl binary (kubernetes deployments)


## KOPS BINARY SETUP
### 1. Download the latest Kops binary:
   curl -Lo kops https://github.com/kubernetes/kops/releases/download/$(curl -s https://api.github.com/repos/kubernetes/kops/releases/latest | grep tag_name | cut -d '"' -f 4)/kops-linux-amd64
   
### 2. Make the binary executable:
   chmod +x ./kops

### 3. Move the binary to a directory in your PATH:
   sudo mv ./kops /usr/local/bin/

## KUBECTL BINARY SETUP
### 1. Download the latest Kubectl binary:
   curl -Lo kubectl https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl

### 2. Make the binary executable:
   chmod +x ./kubectl

### 3. Move the binary to a directory in your PATH:
   sudo mv ./kubectl /usr/local/bin/kubectl

## SETUP IAM USER (kops access aws resources)
### This is awscli commands working methods. kindly configure aws-cli packages in your linux machines.

In order to build clusters within AWS we'll create a dedicated IAM user for kops. This user requires API credentials in order to use kops. Create the user, and credentials, using the AWS console.
The kops user will require the following IAM permissions to function properly: or You can create now with admin access for testing.
   1. AmazonEC2FullAccess
   2. AmazonRoute53FullAccess
   3. AmazonS3FullAccess
   4. IAMFullAccess
   5. AmazonVPCFullAccess
   

## configure the aws client to use your new IAM user
    aws configure            Use your new access and secret key here
    aws iam list-users       you should see a list of all your IAM users here



   ### export AWS_ACCESS_KEY_ID=$(aws configure get aws_access_key_id)
   ### export AWS_SECRET_ACCESS_KEY=$(aws configure get aws_secret_access_key)


## Prepare local environment

### We're ready to start creating our first cluster! Let's first set up a few environment variables to make this process easier.
 
 export NAME=venkat.k8s.local
 
 export KOPS_STATE_STORE=s3://bucket-name
 
 kops create cluster --zones ap-south-1a ${NAME}
 
### LIST CLUSTER DETAILS
   kops get cluster
    
### VALIDATE CLUSTER
  kops validate cluster

 ### To Delete the cluster 
 kops delete cluster --name=venkat.k8s.local --state=s3://bucket-name –yes
