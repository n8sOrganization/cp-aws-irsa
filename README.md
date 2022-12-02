# Example crossplane config for AWS EKS IRSA

vRelevant blog post: https://vrelevant.net/crossplane-all-the-patches-with-aws-irsa-config/

## Prerequisites
 1. K8s cluster
 2. kubectl cli
 3. AWS account
 4. aws cli

(All steps assume you are working from the root of the repo clone.)

### 1. Install Crossplane
```console 
kubectl create namespace crossplane-system
```

```console
helm repo add crossplane-stable https://charts.crossplane.io/stable
```
 
```console 
helm repo update
```
 
```console
helm install crossplane --namespace crossplane-system crossplane-stable/crossplane
```

### 2. Install XRDs and Compositions

```console
kubectl apply -k config/. 
```
 
### 3. Create cred config for provider-aws and configure provider
```console 
AWS_PROFILE=default && echo -e "[default]\naws_access_key_id = $(aws configure get aws_access_key_id --profile $AWS_PROFILE)\naws_secret_access_key = $(aws configure get aws_secret_access_key --profile $AWS_PROFILE)" > creds.conf
```

```console
kubectl create secret generic aws-creds -n crossplane-system --from-file=credentials=./creds.conf
```

```console 
kubectl apply -f provider/aws-providerConfig.yaml
```

### 4. Deploy

```console
kubectl create -f examples/cluster-claim.yaml
```

```console
watch kubectl get managed
```

