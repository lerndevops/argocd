## ArogCD CLI 

#### You can interact with Argo CD via 
* CLI 
* WEB UI
* Rest/gRPC API 

#### CLI is useful when you need to interact with ArgoCD in CI Pipelines 

## ArogCD CLI - Commands 

#### You can manage everything using CLI
* Manage Applications 
* Manage Repos 
* Maanage Clusters 
* Admin tasks 
* Manage Projects 
* etc...


## Install ArgoCD CLI 

#### Install Argo CD CLI on your machine.
#### Follow official docs for your preferred platform (linux or mac or  windows)
* https://argo-cd.readthedocs.io/en/stable/cli_installation/
#### Expose Argo CD Server whether using NodePort or port forward or ingress or load balancer service.

## ArgoCD CLI - Login 

#### You need to login to ArogCD Server before using any command 
```sh
argocd login <ArgoCD_Server_IP:PORT>/<URL>
```

#### After successful login you can try using commands 
```sh
argocd cluster list 
```


