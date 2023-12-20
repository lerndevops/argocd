## repos used in argocd

#### Public Repos 
* **Public Git repos can be used directly in application.**
* **Public standard Helm repos can be used directly in  application.**


#### Private Repos 

#### Git
---
* **Private repos needs to be registered in ArgoCD with proper authentication before using it in applications.**
* **ArgoCD support connecting to private repos using below  ways:**
   * HTTPs: using username and password or access token.
   * SSH: using ssh private key.
   * GitHub / GitHub Enterprise : GitHub App credentials.
* **Private repos credentials are stored in normal k8s secrets.**
* **You can register repos using declarative approach, cli and  web UI.**

#### Helm
---
* **Non standard Helm repositories have to be registered  explicitly.**
* **Private Helm repos needs to be registered in ArgoCD with  proper authentication before using it in applications.**
* **ArgoCD support connecting to private Helm repos using**
    * username/password
    * tls cert/key
* **Registering Helm repos in ArgoCD can be done declaratively, CLI and Web UI.**


## create/add Repo declaratively using yaml 

#### GitHub Repo using HTTPS

```yaml 
apiVersion: v1
kind: Secret
metadata:
  name: private-repo  
  namespace: argocd  
  labels:
    argocd.argoproj.io/secret-type: repository
stringData:
  type: git
  url: https://github.com/argoproj/private-repo  
  password: my-password
  username: my-username
```

#### GitHub Repo using HTTPS - insecure

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: private-repo
  namespace: argocd
  labels:
    argocd.argoproj.io/secret-type: repository  
stringData:
  type: git
  url: https://github.com/argoproj/private-repo
  password: my-password
  username: my-username  
  insecure: true
```
#### GitHub Repo using HTTPS an tls
```yaml 
apiVersion: v1
kind: Secret
metadata:
  name: private-repo  
  namespace: argocd
  labels:
    argocd.argoproj.io/secret-type: repository
stringData:
  type: git
  url: https://github.com/argoproj/private-repo  
  password: my-password
  username: my-username
  tlsClientCertData: ...
  tlsClientCertKey: ...
```

#### GitHub Repo Using SSH

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: private-repo
  namespace: argocd
  labels:
    argocd.argoproj.io/secret-type: repository
stringData:
  type: git
  url: https://github.com/argoproj/private-repo
  sshPrivateKey: |
   -----BEGIN OPENSSH PRIVATE KEY-----
   ...
   -----END OPENSSH PRIVATE KEY-----
```
#### GitHub Repo Using GitHub APP
```yaml 
apiVersion: v1
kind: Secret
metadata:
  name: private-repo
  namespace: argocd
  labels:
    argocd.argoproj.io/secret-type: repository
stringData:
  type: git
  url: https://github.com/argoproj/private-repo
  githubAppID: 1
  githubAppInstallationID: 2
  githubAppPrivateKey: |
   -----BEGIN OPENSSH PRIVATE KEY-----
   ...
   -----END OPENSSH PRIVATE KEY-----
```

#### Helm Repo 
```yaml 
apiVersion: v1
kind: Secret
metadata:
  name: private-repo
  namespace: argocd  labels:
  argocd.argoproj.io/secret-type: repository
stringData:
  type: helm  
  name: argo
  url: https://argoproj.github.io/argo-helm
  password: my-password
  username: my-username  
  tlsClientCertData: ""
  tlsClientCertKey: ""
```

## create/add Repo declaratively using CLI

```sh
# Public Repo
argocd repo add https://lerndevops.github.io/helm-charts/ --type helm --name  lerndevops
```

```sh
# Private Repo
argocd repo add https://lerndevops.github.io/helm-charts/ --type helm --name  lerndevops --username test --password test
```
