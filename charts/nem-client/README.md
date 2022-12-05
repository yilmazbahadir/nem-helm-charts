# nem-client

![Version: 0.1.0](https://img.shields.io/badge/Version-0.1.0-informational?style=flat-square) ![Type: application](https://img.shields.io/badge/Type-application-informational?style=flat-square)

nem-client Helm chart for Kubernetes

**Homepage:** <https://github.com/NemProject/nem>

## Source Code

* <https://github.com/NemProject/nem>

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
helm install testnet ./charts/nem-client --create-namespace --namespace=testnet --set-file config.customNemesisFileBase64= PATH_TO_FILE/nemesis-base64.txt --set clusterName=$(kubectl config view -o jsonpath='{.clusters[].name}{"\n"}') --set config.user.nem.network=testnet --set config.user.nis.bootKey=PrivateKey --set config.user.nis.bootName=MyNodeName
```

## Values

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| affinity | object | `{}` | Affinity configuration for pods |
| clusterName | string | `"docker-desktop"` |  |
| config | object | See `values.yaml` | Config object for nisproperties files |
| config.customNemesisFileBase64 | string | `nil` | base64 encoded nemesis.bin file content |
| config.dbProperties | string | `nil` | nis-client db.properties file content as multi-line string |
| config.logalphaProperties | string | `nil` | nis-client logalpha.properties file content as multi-line string |
| config.peersConfigJson | string | `nil` | nis-client peers-config.json file content as multi-line string |
| config.user | object | See `values.yaml` | config-user.properties |
| config.user."nem.host" | string | `"127.0.0.1"` | hostname/ip e.g. example.com |
| config.user."nem.httpPort" | int | `7890` | api/peer http port |
| config.user."nem.httpsPort" | int | `7891` | api/peer https port |
| config.user."nem.network" | string | `"mainnet"` | nem network: mainnet|testnet |
| config.user."nem.network.nemesisFilePath" | string | `nil` | nemesis.bin file path, leave empty when no custom nemesis files, set to /usersettings/nemesis.bin otherwise |
| config.user."nem.websocketPort" | int | `7778` | api/peer websocket port |
| config.user."nis.bootKey" | string | `nil` | main account private key @default new account will be generated and set |
| config.user."nis.bootName" | string | `nil` | name of the NIS node |
| containerSecurityContext | object | `{}` | The security context for containers |
| extraContainerPorts | list | `[]` | Additional ports for the main container |
| extraContainers | list | `[]` | Additional containers |
| extraEnv | list | `[]` | Additional env variables |
| extraPorts | list | `[]` | Additional ports. Useful when using extraContainers or extraContainerPorts |
| extraVolumeMounts | list | `[]` | Additional volume mounts |
| extraVolumes | list | `[]` | Additional volumes |
| fullnameOverride | string | `""` | Overrides the chart's computed fullname |
| image.pullPolicy | string | `"IfNotPresent"` | nis-client image pull policy |
| image.repository | string | `"nis-client"` | nis-client image repository |
| image.tag | string | `"latest"` | nis-client image tag |
| imagePullSecrets | list | `[]` | Image pull secrets for Docker images |
| ingress.annotations | object | `{}` |  |
| ingress.className | string | `""` |  |
| ingress.enabled | bool | `false` |  |
| ingress.hosts[0].host | string | `"localhost"` |  |
| ingress.hosts[0].paths[0].path | string | `"/"` |  |
| ingress.hosts[0].paths[0].pathType | string | `"ImplementationSpecific"` |  |
| ingress.tls | list | `[]` |  |
| initChownData.enabled | bool | `true` | Init container to set the correct permissions to access data directories |
| initChownData.image.pullPolicy | string | `"IfNotPresent"` | Container pull policy |
| initChownData.image.repository | string | `"busybox"` | Container repository |
| initChownData.image.tag | string | `"1.34.0"` | Container tag |
| initChownData.resources | object | `{}` | Resource requests and limits |
| initContainers | list | `[]` | Additional init containers |
| livenessProbe | object | See `values.yaml` | Liveness probe |
| nameOverride | string | `""` | Overrides the chart's name |
| nodeSelector | object | `{}` |  |
| podAnnotations | object | `{}` | Pod annotations |
| podSecurityContext | object | See `values.yaml` | The security context for pods |
| readinessProbe | object | See `values.yaml` | Readiness probe |
| replicaCount | int | `1` | Number of replicas |
| resources | object | `{}` |  |
| service.type | string | `"LoadBalancer"` | Service type: ClusterIP|LoadBalancer|NodePort |
| serviceAccount.annotations | object | `{}` | Annotations to add to the service account |
| serviceAccount.create | bool | `true` | Specifies whether a service account should be created |
| serviceAccount.name | string | `""` | The name of the service account to use. If not set and create is true, a name is generated using the fullname template |
| tolerations | list | `[]` | Tolerations for pods # ref: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/ |
| updateStrategy | object | `{"type":"RollingUpdate"}` | Update strategy for the StatefulSet |
| updateStrategy.type | string | `"RollingUpdate"` | Update strategy type |
