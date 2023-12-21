### APP of APPS - Managing many apps using one app
---
* ### Managing ArgoCD Apps manually using kubectl is not so effective, and can be  solved by using app of apps pattern.
* ### App of apps: Create one ArgoCD App that responsible of managing other apps.
* ### Your can structure app of apps in many options
   * #### Root app that is tracking directory of ArgoCD apps manifests with enabling the recursion.
      * ##### Any new app added to the tracked directory can be synced by ArgoCD directly.
   * #### Root app that is tracking helm chart that include all other apps.
      * ##### Adding new apps will be within the helm chart , then it can be synced by ArgoCD directly.
 