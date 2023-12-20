## Create ArgoCD Application 

### Using CLI 
---
#### Step1: login to ArgoCD using CLI 
```sh
## Assuming that you have already install ArgoCD CLI

argocd login <argocd-server-ip(url):port> --username <your-username> --password <your-pass>

# ex: argocd login 10.128.0.2:31738 --username admin --password mypassword
```
#### Setp2: create application using Directory of Yamls

#### using CLI 
---
```sh 
argocd app create java-app --repo https://github.com/lerndevops/argocd.git --path 05-applications-and-projects/directory-of-files/java-app --dest-server https://kubernetes.default.svc --dest-namespace default
```
#### Using Declarative YAML 
---
```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: java-app
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/lerndevops/argocd.git
    path: 05-applications-and-projects/directory-of-files/java-app
    targetRevision: main
    directory:
     recurse: true
  destination:
    namespace: default
    server: https://kubernetes.default.svc
  #syncPolicy:
  #  syncOptions:
  #    - CreateNamespace=true
  #    - PruneLast=true
  #  retry:
  #    limit: 2
  #    backoff:
  #      duration: 5s
  #      factor: 2
  #      maxDuration: 3m0s
```