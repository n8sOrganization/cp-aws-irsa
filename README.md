# Example crossplane config for AWS EKS IRSA

_Note: The better practice would be to use a Crossplane Configuration Package to apply this config. But that requires an image registry with a trusted and valid signed cert. To make it easier to apply this config, I am prroviding instructions to apply the required manifests via Kustomize._

## Prerequities
 1. K8s cluster
 2. kubectl cli
 3. AWS account
 4. aws cli

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

### 3. Create creds and config for provider-aws
```console 
AWS_PROFILE=default && echo -e "[default]\naws_access_key_id = $(aws configure get aws_access_key_id --profile $AWS_PROFILE)\naws_secret_access_key = $(aws configure get aws_secret_access_key --profile $AWS_PROFILE)" > creds.conf
```

```console
kubectl create secret generic aws-creds -n upbound-system --from-file=credentials=./creds.conf
```

```console 
kubectl apply -f examples/provider-aws.yaml
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

