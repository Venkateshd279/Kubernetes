# EKS Cluster creation (Method 2)

### Pre request

- AWS CLI 
- Kubectl 
- Required IAM role 

```bash 
    aws sts get-caller-identity
```

## Step 1: Create your Amazon EKS cluster

```bash 
    aws cloudformation create-stack \
  --region us-east-1 \
  --stack-name my-eks-vpc-stack \
  --template-url https://s3.us-west-2.amazonaws.com/amazon-eks/cloudformation/2020-10-29/amazon-eks-vpc-private-subnets.yaml
```

## Step 2: Create IAM policies 

Copy the following contents to a file named `eks-cluster-role-trust-policy.json`.

```json 
    {
  "Version":"2012-10-17",		 	 	 
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "eks.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

b. create role 

```bash 
    aws iam create-role \
  --role-name myAmazonEKSClusterRole \
  --assume-role-policy-document file://"eks-cluster-role-trust-policy.json"
```

c. Attach the required Amazon EKS managed IAM policy to the role.

```bash 
    aws iam attach-role-policy \
  --policy-arn arn:aws:iam::aws:policy/AmazonEKSClusterPolicy \
  --role-name myAmazonEKSClusterRole
```

## Step 3: Create EKS cluster 

-   cluster name: my-cluster
-   Cluster Service Role: myAmazonEKSClusterRole and Choose Next   
-   VPC: * | my-eks-vpc-stack-VPC
-   subnet: * | my-eks-vpc-stack-*
-   Security group: * | my-eks-vpc-stack-ControlPlaneSecurityGroup-* and choose Next
-   On the Configure observability page, choose Next.
-   On the Select add-ons page, choose Next.
-   On the Configure selected add-ons settings page, choose Next.
-   On the Review and create page, choose Create.

## Step 4: Connect your EKS cluster 

```bash 
    aws eks update-kubeconfig --region us-east-1 --name my-cluster
```

### 4.1 Test your connectivity

```bash
    kubectl get svc
```
```bash 
    #example output 
    NAME             TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
    svc/kubernetes   ClusterIP   10.100.0.1   <none>        443/TCP   1m
```

## Step 5: Create your nodegroup IAM role 

a. Copy the following contents to a file named `node-role-trust-policy.json`.

```json
    {
  "Version":"2012-10-17",		 	 	 
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "ec2.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

b. Create the node IAM role.

```bash 
    aws iam create-role \
  --role-name myAmazonEKSNodeRole \
  --assume-role-policy-document file://"node-role-trust-policy.json"
```

c. Attach the required managed IAM policies to the role.

```bash 

    aws iam attach-role-policy \
  --policy-arn arn:aws:iam::aws:policy/AmazonEKSWorkerNodePolicy \
  --role-name myAmazonEKSNodeRole
aws iam attach-role-policy \
  --policy-arn arn:aws:iam::aws:policy/AmazonEC2ContainerRegistryReadOnly \
  --role-name myAmazonEKSNodeRole
aws iam attach-role-policy \
  --policy-arn arn:aws:iam::aws:policy/AmazonEKS_CNI_Policy \
  --role-name myAmazonEKSNodeRole
```

## Step 6: Create your nodegroup 

-   Choose your cluster and choose `compute` tab
-   Add nodegroup
-   Name: `my-nodegroup`
-   Node IAM role name: `myAmazonEKSNodeRole` and Choose `Next`
-   On the Set compute and scaling configuration page, accept the default values and choose Next.
-   On the Specify networking page, accept the default values and choose Next.
-   On the Review and create page, review your managed node group configuration and choose Create.
-   After several minutes, the Status in the Node Group configuration section will change from Creating to Active. Don’t continue to the next step until the    status is Active. 

## Step 7: Delete EKS Cluster 
-   Delete your nodegroup
-   Delete your cluster
-   Delete your cloudformation stack [AWS CloudFormation](https://console.aws.amazon.com/cloudformation/)
-   Choose the my-eks-vpc-stack stack, and then choose Delete.
-   In the Delete my-eks-vpc-stack confirmation dialog box, choose Delete stack.
-   Open IAM console [IAM](https://console.aws.amazon.com/iam/)
-   Navigate to Roles `myAmazonEKSClusterRole ` and `myAmazonEKSNodeRole` delete it. 



