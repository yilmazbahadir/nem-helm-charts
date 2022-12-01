# nem-client Helm Charts
This repo contains Helm charts for the [nem-client](https://github.com/NemProject/nem) component.

## Requirements
- Kubernetes(K8s) v1.25.2
  - ingress-nginx controller plugin
  ```bash
  kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.4.0/deploy/static/provider/cloud/deploy.yaml
  ```
- Helm v3.10.1

## Installation

### Deployment
To deploy nem-client helm chart to local K8s environment for mainnet:
```bash
helm install mainnet ./charts/nem-client --create-namespace --namespace=mainnet --set clusterName=$(kubectl config view -o jsonpath='{.clusters[].name}{"\n"}') --set config.user.nem.network=mainnet --set config.user.nis.bootKey=PrivateKey --set config.user.nis.bootName=MyNodeName
```
To list the Helm packages:
```bash
helm ls --namespace=mainnet
```

To check the K8s resources:
```bash
kubectl get all --namespace=mainnet
```

To uninstall the Helm package:
```bash
helm uninstall mainnet --namespace=mainnet
```

### Testnet Deployment
To deploy nem-client helm chart to local K8s environment for testnet:
```bash
helm install testnet ./charts/nem-client --create-namespace --namespace=testnet --set clusterName=$(kubectl config view -o jsonpath='{.clusters[].name}{"\n"}') --set config.user.nem.network=testnet --set config.user.nis.bootKey=PrivateKey --set config.user.nis.bootName=MyNodeName
```
Please do not forget to add `--namespace=testnet` to all of the subsequent commands.

### Custom Testnet Deployment with Generated Nemesis File
Use [nemesis-generator](https://github.com/NemProject/nemesis-generator) to generate a custom nemesis file for the initial block.

For testnet deployment with custom nemesis binary:
1. Encode nemesis binary file with Base64 to a (nemesis-base64.)txt file
```bash
base64 -i nemesis.bin -o PATH_TO_FILE/nemesis-base64.txt
```
2. Install helm chart by providing custom nemesis binary file as a value(--set-file):

```bash
helm install testnet ./charts/nem-client --create-namespace --namespace=testnet --set-file config.user.customNemesisFileBase64= PATH_TO_FILE/nemesis-base64.txt --set clusterName=$(kubectl config view -o jsonpath='{.clusters[].name}{"\n"}') --set config.user.nem.network=testnet --set config.user.nis.bootKey=PrivateKey --set config.user.nis.bootName=MyNodeName
```