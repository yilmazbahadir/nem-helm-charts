# nem-client

![Version: 0.1.0](https://img.shields.io/badge/Version-0.1.0-informational?style=flat-square) ![Type: application](https://img.shields.io/badge/Type-application-informational?style=flat-square)

nem-client Helm chart for Kubernetes

**Homepage:** <https://github.com/NemProject/nem>

## Source Code

* <https://github.com/NemProject/nem>

## Requirements
- [Kubernetes(K8s) v1.25.2](https://kubernetes.io/docs/setup/)
   - if ingress is needed then
     1. set `.Values.ingress.enabled` to `true`
     2. install an Ingress controller from [Ingress Controllers](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/) list
        - If you pick `ingress-nginx` controller, you need to
  create a file named `values-nginx-controller.yaml` with the following content:
          ```yaml
            # configure the tcp configmap
            tcp:
              7778: localhost:7778

            # enable the service and expose the tcp ports.
            # be careful as this will potentially make them
            # available on the public web
            controller:
              service:
                enabled: true
                ports:
                  http: 7890
                  https: 7891
                targetPorts:
                  http: http
                  https: https
          ```
          And run the following commands:
          ```bash
          helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
          helm repo update
          helm install ingress-nginx ingress-nginx/ingress-nginx --create-namespace --namespace=ingress-nginx -f ./values-nginx-controller.yaml
          ```
- [Helm](https://helm.sh/docs/intro/install/) v3.10.1
- K8s [Local Path Provisioner](https://github.com/rancher/local-path-provisioner)(v0.0.23) (if `.Values.persistence.enabled` is `true`(**default**))
  ```bash
  kubectl apply -f https://raw.githubusercontent.com/rancher/local-path-provisioner/v0.0.23/deploy/local-path-storage.yaml
  ```

## Installation

### Deployment
To deploy nem-client helm chart to local K8s environment for mainnet:
```bash
helm install mainnet ./charts/nem-client --create-namespace --namespace=mainnet --set config.user.nem.network=mainnet --set config.user.nis.bootKey=PrivateKey --set config.user.nis.bootName=MyNodeName
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
helm install testnet ./charts/nem-client --create-namespace --namespace=testnet --set config.user.nem.network=testnet --set config.user.nis.bootKey=PrivateKey --set config.user.nis.bootName=MyNodeName
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
helm install testnet ./charts/nem-client --create-namespace --namespace=testnet --set-file config.customNemesisFileBase64= PATH_TO_FILE/nemesis-base64.txt --set config.user.nem.network=testnet --set config.user.nis.bootKey=PrivateKey --set config.user.nis.bootName=MyNodeName
```

## Values

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| affinity | object | `{}` | Affinity configuration for pods |
| annotations | object | `{}` | Annotations for the StatefulSet |
| config | object | See `values.yaml` | Config object for nisproperties files |
| config.customNemesisFileBase64 | string | `nil` | base64 encoded nemesis.bin file content |
| config.dbProperties | string | `nil` | nis-client db.properties file content as multi-line string |
| config.logalphaProperties | string | `nil` | nis-client logalpha.properties file content as multi-line string |
| config.peersConfigJson | string | `nil` | nis-client peers-config.json file content as multi-line string |
| config.user | object | See `values.yaml` | config-user.properties |
| config.user."nem.folder" | string | `"/app/data"` | folder database and log files should be located |
| config.user."nem.host" | string | `"127.0.0.1"` | hostname/ip e.g. example.com |
| config.user."nem.httpPort" | int | `7890` | api/peer http port |
| config.user."nem.httpsPort" | int | `7891` | api/peer https port |
| config.user."nem.network" | string | `"mainnet"` | mainnet|testnet |
| config.user."nem.network.nemesisFilePath" | string | `nil` | nemesis.bin file path, leave empty when no custom nemesis files, set to /usersettings/nemesis.bin otherwise |
| config.user."nem.websocketPort" | int | `7778` | api/peer websocket port |
| config.user."nis.bootKey" | string | `nil` | main account private key @default new account will be generated and set |
| config.user."nis.bootName" | string | `nil` | name of the NIS node |
| containerSecurityContext | object | `{}` | The security context for containers |
| extraContainerPorts | list | `[]` | Additional ports for the main container |
| extraContainers | list | `[]` | Additional containers |
| extraEnv | list | `[]` | Additional env variables |
| extraPorts | list | See `values.yaml` | Additional ports. Useful when using extraContainers or extraContainerPorts |
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
| initChownData.image.tag | string | `"1.35.0"` | Container tag |
| initChownData.resources | object | `{}` | Resource requests and limits |
| initContainers | list | `[]` | Additional init containers |
| livenessProbe | object | See `values.yaml` | Liveness probe |
| nameOverride | string | `""` | Overrides the chart's name |
| nodeSelector | object | `{}` |  |
| persistence.accessModes | list | `["ReadWriteOnce"]` | Access mode for the volume claim template |
| persistence.annotations | object | `{"helm.sh/resource-policy":"keep"}` | Annotations for volume claim template |
| persistence.enabled | bool | `true` | Uses an EmptyDir when not enabled |
| persistence.existingClaim | string | `nil` | Use an existing PVC when persistence.enabled |
| persistence.selector | object | `{}` | Selector for volume claim template |
| persistence.size | string | `"8Gi"` | Requested size for volume claim template |
| persistence.storageClassName | string | `"local-path"` | Use a specific storage class E.g 'local-path' for local storage to achieve best performance Read more (https://github.com/rancher/local-path-provisioner) |
| podAnnotations | object | `{}` | Pod annotations |
| podLabels | object | `{}` | Pod labels |
| podManagementPolicy | string | `"OrderedReady"` | Pod management policy |
| podSecurityContext | object | See `values.yaml` | The security context for pods |
| rbac.clusterRules | list | See `values.yaml` | Required ClusterRole rules |
| rbac.create | bool | `true` | Specifies whether RBAC resources are to be created |
| rbac.rules | list | See `values.yaml` | Required ClusterRole rules |
| readinessProbe | object | See `values.yaml` | Readiness probe |
| resources | object | `{}` |  |
| service.annotations | object | `{}` |  |
| service.type | string | `"ClusterIP"` | Service type: ClusterIP|LoadBalancer|NodePort |
| serviceAccount.annotations | object | `{}` | Annotations to add to the service account |
| serviceAccount.create | bool | `true` | Specifies whether a service account should be created |
| serviceAccount.name | string | `""` | The name of the service account to use. If not set and create is true, a name is generated using the fullname template |
| terminationGracePeriodSeconds | int | `300` | The application is given a certain amount of time to shutdown before it's terminated forcefully |
| tolerations | list | `[]` | Tolerations for pods # ref: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/ |
| updateStrategy | object | `{"type":"RollingUpdate"}` | Update strategy for the StatefulSet |
| updateStrategy.type | string | `"RollingUpdate"` | Update strategy type (RollingUpdate|OnDelete) |
