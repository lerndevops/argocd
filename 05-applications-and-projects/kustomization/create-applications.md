## Create ArgoCD Application 

### Using CLI 
---
#### Step1: login to ArgoCD using CLI 
```sh
## Assuming that you have already install ArgoCD CLI

argocd login <argocd-server-ip(url):port> --username <your-username> --password <your-pass>

# ex: argocd login 10.128.0.2:31738 --username admin --password mypassword
```
#### Setp2: create application using Helm 

#### using CLI 
---
```sh 
argocd app create pyapp --repo https://github.com/lerndevops/argocd.git --path 05-applications-and-projects/kustomization/pyapp --dest-server https://kubernetes.default.svc --revision main --dest-namespace default --nameprefix test
```
#### Using Declarative YAML 
---
```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: pyapp
  namespace: argocd
spec:
   destination:
     namespace: pyapp
     server: "https://kubernetes.default.svc"
   project: default
   source:
     path: 05-applications-and-projects/kustomization/pyapp
     repoURL: "https://github.com/lerndevops/argocd.git"
     targetRevision: HEAD
     kustomize:
       namePrefix: "test-"
   syncPolicy:
     syncOptions:
       - CreateNamespace=true
```