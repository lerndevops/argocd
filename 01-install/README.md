> **Note:** 
1. Argo CD Components are deployed as applications/pods running inside a kuberetes Cluster 
2. it is neccessary that you are familiar on how to run applications in kubernetes cluster

## Install the Argo CD 

> **pre-req:**
1. A working kubernetes cluster 
2. familiarity with kubectl command line utility 

```sh
kubectl create namespace argocd
```
```sh
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

> #### you can follow Argo CD "[Getting Started](https://argo-cd.readthedocs.io/en/stable/getting_started/)" Guides for more details 


## Install Argo CD CLI

> Argo CD CLI can be installed on any of the OS like linux,windows,mac 

#### [find the installation instruction from official documents here](https://argo-cd.readthedocs.io/en/stable/cli_installation/)

### Linux installation here
```sh
cd $HOME
curl -sSL -o argocd-linux-amd64 https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
sudo install -m 555 argocd-linux-amd64 /usr/local/bin/argocd
rm argocd-linux-amd64
```
```sh
#validate 
argocd version --client
```

## Expose & Update default password for Argo CD 

> **By default, the Argo CD UI (API server) is not exposed with an external IP. To access the API server, expose the Argo CD API server with NodePort Service:**

### 1. Change the argocd-server service type to NodePort:
```sh 
kubectl patch service argocd-server -n argocd -p '{"spec": {"type": "NodePort"}}'
```
```sh
#validate 
kubectl get service argocd-server -n argocd
```

### 2. Login Using The CLI

> The initial password for the `admin` account is auto-generated and stored as clear text in the field password in a secret named argocd-initial-admin-secret in your Argo CD installation namespace. 

> You can simply retrieve this password using the argocd CLI:

```sh 
argocd admin initial-password -n argocd
```

### 3. Using the username `admin` and the `password from above,` login to Argo CD's IP or hostname:**

```sh 
argocd login <ARGOCD_SERVER-IP:NodePort>

example: argocd login 10.128.0.2:31738
```

### 4. Change the password using the command:**
```sh
argocd account update-password
```

## Access the Argo CD UI

```sh 
#open browser & hit 

argo-cd-server-ip:nodeport
ex: http://34.136.5.35:31738

use the credential as configured above 

default-user: admin
password: as configured  
```


