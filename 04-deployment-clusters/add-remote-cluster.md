## pre-req: 

* **Ensure the target kubernetes cluter is created & running, then find**
    * cluster ca data 
    * bearer token for authentication
    * give neccessary authorization for the token we using for authentication  

### configure below on target cluster 
---
#### a. create service account 
```yaml 
# vi sa.yaml
kind: ServiceAccount
apiVersion: v1
metadata:
  name: argocd-deployments
  namespace: kube-system 
```
```sh
kubectl apply -f sa.yaml
```

#### b. create a secret 
```yaml 
## Create a secret and specify the name of the service account as annotations within the metadata section.
## vi argocd-deployments-token.yaml 
---
apiVersion: v1
kind: Secret
metadata:
  name: argocd-deployments-token
  namespace: kube-system
  annotations:
    kubernetes.io/service-account.name: argocd-deployments
type: kubernetes.io/service-account-token
```
```sh 
kubectl apply -f argocd-deployments-token.yaml
```
#### c. create clusterrole, clusterrolebinding with neccessary previliges to ServiceAccount 
```yaml 
# vi token-authorization.yaml 
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: argocd-deployments-cr
rules:
- apiGroups:
  - '*'
  resources:
  - '*'
  verbs:
  - '*'
- nonResourceURLs:
  - '*'
  verbs:
  - '*'
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: argocd-deployments-cr-binding
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: argocd-deployments-cr
subjects:
- kind: ServiceAccount
  name: argocd-deployments
  namespace: kube-system
```
```sh
kubectl apply -f token-authorization.yaml 
```

#### d. extract the required values (token & ca ) now from secret we created above 

```sh
# get the ca base64 data 
kubectl -n kube-system get secret/argocd-deployments-token -o jsonpath='{.data.ca\.crt}' ; echo
```
```sh 
# get the bearer token data
kubectl -n kube-system get secret/argocd-deployments-token -o jsonpath='{.data.token}' | base64 -d
```
```sh 
# get the namespace 
kubectl -n kube-system get secret/argocd-deployments-token -o jsonpath='{.data.namespace}' | base64 -d ; echo
```

## Now, Lets add the target cluster to ArgoCD using Declarative Yaml 

> goto the argo cd installed kubernetes cluter 
> ensure you have the access to run "kubectl" commands 

```yaml  
# vi stag-cluster.yaml
---
apiVersion: v1
kind: Secret
metadata:
  name: stag-cluster
  namespace: argocd
  labels:
    argocd.argoproj.io/secret-type: cluster
stringData:
  name: stag-cluster
  server: https://10.128.0.7:6443
  config: |
    {
       "bearerToken": "eyJhbGciOiJSUzI1NiIsImtpZCI6IkF6T3NlZ1VnUG9WVzRMVWIyc0lVb2d5aVpFYmc2YTBGWEltM2huR0NELTAifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlLXN5c3RlbSIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJhcmdvY2QtZGVwbG95bWVudHMtdG9rZW4iLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC5uYW1lIjoiYXJnb2NkLWRlcGxveW1lbnRzIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQudWlkIjoiNDYzODA3NzctMzdlYi00YTRhLThjY2ItZDk0M2M1YTc2ZDhjIiwic3ViIjoic3lzdGVtOnNlcnZpY2VhY2NvdW50Omt1YmUtc3lzdGVtOmFyZ29jZC1kZXBsb3ltZW50cyJ9.zLc8l4v1lqorhgVCCczXqQLjyO_Sm_JS1ngLXIzMUlflmh0q0QG6U-v3smzJ7zNTrpgPRCBE81Cm37hWqNmf1OW_Cr4DB8ZoySbAMD7UHFqu4LXlRw9B-FT_sYzh_rWGw1mEIVw8ZPPjrUuWIiPIRDAW98FmnhrasoO249n8lqhFOiq8ls5xeNcJFJh6As2ojMrx_2-NoAuZOOmsxEVNpVzdLykrbheXHVven5VXzy3xeLPWA2oGSBpUlgvdzsoCKRYi-Tjcdd2BaRu_kGN90ZPJHqbrERGeLtqqmnpM6t9WxG9nM0lmLnizIC2WKadJ_BNmJ0zeQLniib11TZ-PfQ",
       "tlsClientConfig": {
       "insecure": false,
       "caData": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURCVENDQWUyZ0F3SUJBZ0lJYjAwQWdueFpxVmN3RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TXpFeU1EVXdOekU0TkRGYUZ3MHpNekV5TURJd056SXpOREZhTUJVeApFekFSQmdOVkJBTVRDbXQxWW1WeWJtVjBaWE13Z2dFaU1BMEdDU3FHU0liM0RRRUJBUVVBQTRJQkR3QXdnZ0VLCkFvSUJBUUM5WnYzcjVhVHJ2bnpJK1BEaDd1TW42MEozYytDM05RVFJTVW0xWVRKWWZMV0g4bXBEd0dTK2YxMlIKM0toVENsbERUUkxtbEs2ZmFVUVVpWnJNaGJnYXF3eTlXeTkvZDFaY05zRVAvYWZYUHZZWFZ3MWg4Vzhvay8zRApRdjVzcDhPUmZuYU5GK1hIOXQ4VjVseEkvU0JCZUFjNUphaW1YRGtFclVjcHRxZHNkNmxIVWFab25ya1ZlQUJDCjhVSzMrenZUejdPMis0c1NEMlVYdlhmR1oxbEVPNWlEN1lPMzNDVFNQQ0k5ZHF2cUFCejBBNW1IOU9QN0UzQWYKRGhqa2taQ2lCNlArNkRtenlUSGxZTlMvVHlCYTZsMFl4RG52dFRvMU1uS3RmaWQ0Q0poa0VLYU9NdnpMTy9UMwowMnovTGFmV1llbUFycEQ4OHp2KzFGOUNGcHJiQWdNQkFBR2pXVEJYTUE0R0ExVWREd0VCL3dRRUF3SUNwREFQCkJnTlZIUk1CQWY4RUJUQURBUUgvTUIwR0ExVWREZ1FXQkJTTnhTRlVuUmNpV0ozYy9BaFhpcE5hR3R5MTNqQVYKQmdOVkhSRUVEakFNZ2dwcmRXSmxjbTVsZEdWek1BMEdDU3FHU0liM0RRRUJDd1VBQTRJQkFRQU5sNXNMdnhDRgpUNHM5L0Fma2tNUm53OEI1dXl6OXY5OStKeUw1UnRJYlU2dm1aUjBjZVhiUDd0elVyN2paeHNiR3UxQUdndzgwCmdBTGdwODJnTmcvdlpEVDFsTFJaQ0R0M0Y1ZFpzaVRISHhhNi9xQUZJMmY3RDIvcFlZd3BUdXVnRVpoeCt2NE0Kcy9zQ09YdFZ4eUVrSGc0MnBVNlh2MThENDV5eElBdm1IdC9jUlliUkJ4TWFMaW5lRkhQZXlLM3F6Q2N0RU91VQpiclIxQjlRRmhMVkFrSFdyK1BvNkRFZVF2bEUxc1I5Z3Fsb1Fackk5VXBhMFQ0cHBEOVNxUE1ScWt1em4vYWZKCmtkUWh2dnplcHFQemlmWDR6TU5lcTNiUWdWU2lGUmhwMHlLa2xPV1JWYW5ZSVI2ZnFHSDBCRncxYSsrbTlFZHUKVVFidkVPS0prbGxKCi0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K"
       }
    }
```