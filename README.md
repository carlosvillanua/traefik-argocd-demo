# Traefik ArgoCD Demo
This repository will help you get started with Traefik deployment in Kubernetes and allow 
you to leverage OSS tools such as Prometheus, Jaeger for monitoring and Keycloak for
managing API authentication and authorization in k8s using OAuth2.0

## Installation

### Kubernetes Cluster
You can find instructions on how to run this on k3d [here](https://github.com/carlosvillanua/traefik-argocd-demo/blob/main/docs/k3d.md). 

### Traefik

#### Traefik Proxy

Install Traefik using ArgoCD Application CRD

```
kubectl apply -f apps/main/traefik-ingress.yaml -n argocd

```

#### Traefik Dashboard

The traefik admin port can be forwarded locally. Assuming the default traefik namespace is used:

```
NAMESPACE=traefik
kubectl port-forward $(kubectl get pods --selector "app.kubernetes.io/name=traefik" --output=name -n $NAMESPACE) 8080:8080 -n $NAMESPACE

```

This command makes the dashboard accessible through the URL: http://127.0.0.1:8080/dashboard/

#### Supplementary Deployments
##### Observability Stack
- [Prometheus](https://github.com/carlosvillanua/traefik-argocd-demo/tree/main/docs/observability/prometheus.md)
- [Grafana](https://github.com/carlosvillanua/traefik-argocd-demo/tree/main/docs/observability/grafana.md)
- [Tempo](https://github.com/carlosvillanua/traefik-argocd-demo/tree/main/docs/observability/tempo.md)
- [Loki](https://github.com/carlosvillanua/traefik-argocd-demo/tree/main/docs/observability/loki.md)

##### Security Stack
- [OPA](https://github.com/carlosvillanua/traefik-argocd-demo/tree/main/docs/security/opa.md)
- [Keycloak](https://github.com/carlosvillanua/traefik-argocd-demo/tree/main/docs/security/keycloak.md)

#### Examples
Examples folder contains yaml files for deployments, API configuration and traffic generation k6 scripts. Checkout the
[docs](https://github.com/carlosvillanua/traefik-argocd-demo/tree/main/docs/examples/) folder to find out more about what each of these examples contain.

```
kubectl apply -R -f examples/
```
