## ArgoCD Applications 

#### Application is a collection of kubernetes resource definitions (ex: deployments,services,configmaps etc...) for a microservice/application deployed to a cluster 

#### it is defined by two key pieces of information 
* **`Source`:** reference to the desired state in Git(repository, revision, path)
* **`Destination`:** reference to the target cluter and namespace  

#### Applications can be created using below options 
* Declaratively "Yaml". (Recommended)
* Web UI
* CLI 

#### Tools supported for Deploying Applications 

###### ArgoCD supports the below tools as source 

* Helm Charts
* Kustomize Application 
* Directory of Yaml files 
* Jsonnet

## ArogCD Projects 

#### Applications Grouping 

* **projects provide logical grouping of applications (similar to namespace in kubernetes)**
* **can implement access restrictions on projects**
* **useful when ArgoCD is used by multiple Teams**
    * **Allow only specific sources "trusted git repos"**
    * **Allow apps to be deployed into specific clusters & namespaces**
    * **Allow specific resources to be deployed "Deployments, Statefulsets etc.."**

#### Default Project 
* **ArgoCD creates a default project once you install it.**

#### Projects can be created using below options
* Declaratively "Using YAML"
* CLI 
* WEB UI 