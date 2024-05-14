# Tyk ArgoCD Demo
This repository will help you get started with Tyk deployment in Kubernetes and allow 
you to leverage OSS tools such as Prometheus, Jaeger for monitoring and Keycloak for
managing API authentication and authorization in k8s using OAuth2.0

## Installation

### Minikube
You can find instructions on how to run this on Minikube [here](https://github.com/TykTechnologies/tyk-argocd-demo/tree/main/docs/minikube.md). 

### Tyk

Choose between single cluster all-in-one deployment model or distributed deployment model.

#### Tyk (Single cluster all-in-one deployment)

Make sure you have the Tyk Dashboard license key ready and replace "<REPLACE_WITH_DASH_LICENSE>" with it in the command below.

Create secret with Tyk credentials:
```
kubectl create namespace tyk
```

```
kubectl create secret generic tyk-conf --namespace=tyk \
   --from-literal=APISecret=CHANGEME \
   --from-literal=AdminSecret=12345 \
   --from-literal=DashLicense=<REPLACE_WITH_DASH_LICENSE>
```

Install Tyk using ArgoCD Application CRDs

```
kubectl apply -f apps/dependencies
kubectl apply -f apps/observability
kubectl apply -f apps/security
kubectl apply -f apps/main/tyk.yaml
```

You can expose the Tyk Dashboard and Gateway to your localhost using the following command:

```
kubectl port-forward svc/gateway-svc-tyk-gateway --namespace tyk 8080 &
```

```
kubectl port-forward svc/dashboard-svc-tyk-dashboard --namespace tyk 3000 &
```

You can check the state of the Tyk gateway using the following `curl` command:
```
curl localhost:8080/hello
```

You can access Tyk Dashboard here:
```
http://localhost:3000
```

Default admin login is "default@example.com" / "topsecretpassword". It can be changed in the ArgoCD app manifest.

#### Tyk (Distributed deployment)
Please follow the instructions [here](https://github.com/TykTechnologies/tyk-argocd-demo/tree/main/docs/distributed) to install a distributed Tyk deployment.

#### Supplementary Deployments
##### Observability Stack
- [Prometheus](https://github.com/TykTechnologies/tyk-argocd-demo/tree/main/docs/observability/prometheus.md)
- [Grafana](https://github.com/TykTechnologies/tyk-argocd-demo/tree/main/docs/observability/grafana.md)
- [Jaeger](https://github.com/TykTechnologies/tyk-argocd-demo/tree/main/docs/observability/jaeger.md)

Run a k6 load test to generate traffic to view in the above platforms.
```
kubectl apply -f jobs/k6-traffic.yaml
```
##### Security Stack
- [Keycloak](https://github.com/TykTechnologies/tyk-argocd-demo/tree/main/docs/security/keycloak.md)
