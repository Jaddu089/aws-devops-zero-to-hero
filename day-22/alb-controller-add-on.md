# How to setup alb add on

Download IAM policy

```
curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/install/iam_policy.json    ------ >>> this is for ubuntu os

Invoke-WebRequest -Uri "https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/install/iam_policy.json" -OutFile "iam_policy.json"  ----->> this is for windows Powershell

```

Create IAM Policy (ubuntu/powershell)

``` 
aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam_policy.json

aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam_policy.json
```

Create IAM Role

```
eksctl create iamserviceaccount \
  --cluster=<your-cluster-name> \
  --namespace=kube-system \
  --name=aws-load-balancer-controller \
  --role-name AmazonEKSLoadBalancerControllerRole \
  --attach-policy-arn=arn:aws:iam::<your-aws-account-id>:policy/AWSLoadBalancerControllerIAMPolicy \
  --approve

eksctl create iamserviceaccount `
  --cluster dev-personal-cluster `
  --namespace develop `
  --name aws-develop-ns-load-balancer-controller `
  --role-name aws-develop-ns-AmazonEKSLoadBalancerControllerRole `
  --attach-policy-arn arn:aws:iam::129733197923:policy/AWSLoadBalancerControllerIAMPolicy `
  --approve

```

## Deploy ALB controller

Add helm repo

```
helm repo add eks https://aws.github.io/eks-charts
```

Update the repo

```
helm repo update eks
```

Install

```
helm install aws-load-balancer-controller eks/aws-load-balancer-controller \            
  -n kube-system \
  --set clusterName=<your-cluster-name> \
  --set serviceAccount.create=false \
  --set serviceAccount.name=aws-load-balancer-controller \
  --set region=<region> \
  --set vpcId=<your-vpc-id>

helm install aws-load-balancer-controller eks/aws-load-balancer-controller `        
  -n develop `
  --set clusterName=dev-personal-cluster `
  --set serviceAccount.create=false `
  --set serviceAccount.name=aws-load-balancer-ingress-controller `
  --set region=ap-south-1 `
  --set vpcId=vpc-09e38aec24965fa2c
```

Verify that the deployments are running.

```
kubectl get deployment -n kube-system aws-load-balancer-controller
```



