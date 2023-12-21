apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: root-app-helm
  namespace: argocd
  labels:
    app.kubernetes.io/name: root-app-helm
  finalizers:
    #- resources-finalizer.argocd.argoproj.io
    - resources-finalizer.argocd.argoproj.io/background
spec:
  project: default
  source:
    path: 07-app-of-apps/helm/apps-chart
    repoURL: https://github.com/lerndevops/argocd.git
    targetRevision: main
  destination:
    namespace: argocd
    server: https://kubernetes.default.svc
  syncPolicy:
    automated:
      allowEmpty: true
      selfHeal: false
    syncOptions:
    - allowEmpty=true
    - CreateNamespace=true
  ignoreDifferences:
   - kind: Application
     group: argoproj.io
     jsonPointers:
      - /status