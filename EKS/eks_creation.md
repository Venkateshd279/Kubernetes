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

a. Copy the following contents to a file named eks-cluster-role-trust-policy.json.

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


