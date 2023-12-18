## Deployment of Applications into kubernetes clusters from Argo CD

> by default, ArgoCD can deploy Application resources into only local cluster where it is running 

> ArogCD can also be configured to deploy Application resources into remote kubernetes clusters as well 

> we can add remote kubernetes cluters information inluding credentials as k8s secrets 

* Each Secret must have a label as **`argocd.arogproj.io/secret-type: cluster`**
* Each Cluster must have the below data:
    * Name
    * Server (Cluster API Server url)
    * Config (an option to authentication to the cluster)
    * namespaces (optional): comma seperated list of namespaces which are accessiblein that cluster
* You can add remote clusters declaratively or using cli 

### Multiple Options to authenticate to remote clusters

* Basic Authentication (username & password) -- not recommended
* Bearer token Authentication 
* IAM Authentication Configuration (suitable for cloud managed kubernetes clusters)
* External Provider command to supply client credentials 

## Add Cluster Declaratively 

```yaml 
# Ex: Secret YAML file 
---
apiVersion: v1
kind: Secret
metadata:
name: my-cluster
namespace: argocd
labels:
argocd.argoproj.io/secret-type: cluster
stringData:
name: mycluster
server: https://mycluster.com
config: |
{
"bearerToken": "<authentication token>",
"tlsClientConfig": {
"insecure": false,
"caData": "<base64 encoded certificate>"
}
}
```

## Add Cluster using CLI

Note: 
* ensure you logged into argocd api using ArgoCD CLI 
    * ex: argocd login <argocd-api-server-ip:port>.  / use the appropriate credentials

* also, we need to get the target cluster kubeconfig file where we running argocd cli 

```sh 
argocd cluster add context 

# Note: context is the cluster context name that exist in kubeconfig file 
```