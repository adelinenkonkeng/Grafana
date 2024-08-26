## Grafana Installation and Configuration Guide ##

## Kustomize installation ##

How to install grafana-operator using Kustomize

# Flux #

Download Kustomize files
After you have downloaded Flux you can use flux pull artifact to download the manifests.

mkdir grafana-operator
flux pull artifact oci://ghcr.io/grafana/kustomize/grafana-operator:v5.12.0 --output ./grafana-operator/
This will provide you the manifest files unpacked and ready to use.

Kustomize / Kubectl
You can also find the yaml for the cluster_scoped and namespace_scoped release on the release page

Install
Two overlays are provided, for namespace scoped and cluster scoped installation. For more information look at our documentation.

This will install the operator in the grafana namespace.

```kubectl create -f https://github.com/grafana/grafana-operator/releases/latest/download/kustomize-cluster_scoped.yaml```
for a namespace scoped installation

```kubectl create -f https://github.com/grafana/grafana-operator/releases/latest/download/kustomize-namespace_scoped.yaml ```
Patching grafana-operator
When you want to path the grafana operator instead of using kubectl apply you need to use kubectl replace. Else you will get the following error invalid: metadata.annotations: Too long: must have at most 262144 bytes.

For example

kubectl replace -f https://github.com/grafana/grafana-operator/releases/latest/download/kustomize-namespace_scoped.yaml
For more information how kubectl replace works we recommend reading this blog.

## Kustomize ##

latest:

apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

# this will automatically pull the latest release when `kustomize build` is executed
resources:
  - https://github.com/grafana/grafana-operator/releases/latest/download/kustomize-cluster_scoped.yaml
pinned to release:

apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

# update the version to the release you need
resources:
  - https://github.com/grafana/grafana-operator/releases/download/v5.0.10/kustomize-cluster_scoped.yaml
ArgoCD
If you are using ArgoCD you need to add this patch to fix the errors during apply of the CRD.

patches:
  - patch: |-
      apiVersion: apiextensions.k8s.io/v1
      kind: CustomResourceDefinition
      metadata:
        annotations:
          argocd.argoproj.io/sync-options: Replace=true
        name: grafanas.grafana.integreatly.org      
or

patches:
  - patch: |-
      apiVersion: apiextensions.k8s.io/v1
      kind: CustomResourceDefinition
      metadata:
        annotations:
          argocd.argoproj.io/sync-options: ServerSideApply=true
        name: grafanas.grafana.integreatly.org    
