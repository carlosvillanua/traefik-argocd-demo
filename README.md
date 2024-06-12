# Tyk K8S API Platform Teams Demo
This repository will help you get started with Tyk deployment in Kubernetes and allow 
you to leverage OSS tools such as Prometheus, Jaeger for monitoring and Keycloak for
managing API authentication and authorization in k8s using OAuth2.0

## Requirements
1. [minikube](https://minikube.sigs.k8s.io/docs/start/)
2. [kubectl](https://kubernetes.io/docs/tasks/tools/#kubectl)
3. [helm](https://helm.sh/docs/intro/install/)

## Installation

### Minikube & Helm Repositories

Start Minikube
```
minikube start
minikube addons enable ingress
```

### ArgoCD

Install ArgoCD on Minikube

```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

You can expose ArgoCD UI using the following command:
```
kubectl port-forward svc/argocd-server --namespace argocd 8443:443 &
```

You can access the ArgoCD instance in your browser at [localhost:8443](http://localhost:8443):
```
Username: admin
```

You can get the ArgoCD admin password by running the following command:
```
kubectl get secrets -n argocd argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

### Observability Tools

Install observability tools using ArgoCD Application CRDs

```
kubectl apply -f apps/observability
```

### Tyk

Create secret with Tyk credentials:
```
kubectl create secret generic tyk-conf --namespace=tyk \
   --from-literal=APISecret=CHANGEME \
   --from-literal=AdminSecret=12345 \
   --from-literal=DashLicense=eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJhbGxvd2VkX25vZGVzIjoiNzRkM2I5NmYtZjFmYS00ZGFjLTQ4NzUtZDc2ZGRkMWVkMDRiLDA3NjQ4NGNiLTFhYWYtNDZhOS01NTZmLTgyNTdhMWY4YzRkMSw0NmRhNjdkNC03MzVjLTRmMTktNzM4MC1lYzAxM2RkMTQ4MjAsZjhkMGIxOTctYzliNi00MTk1LTUyOWItNzc5MjY0Y2ViZTI5LDI2NmNlMjJiLTg2ZWItNDQ3Ni03NmZhLWNjZjQxY2RkMTI3MCxlYTQ4YjQ0ZS1kYTAwLTQwYTMtNTBlYy02YzZkNjc4NjZjY2IsODRkZjg4ZTAtZTY0ZC00M2QxLTQzZDctNjdlOTAwYjY2NmFkLGViNzVlNTg5LWYwMWEtNGViNy03NWM3LTE4ZWFlYTZkMjc0MSwwZTcwMjc2My1mNjllLTQ4YjYtNDhjMy1lNDFiN2ZlZDBjMDgsYWQ0OWQ1N2ItODg0Zi00ZGJmLTVhN2MtNWE4ZDg2Y2M2ZGE0IiwiZXhwIjoxNzE4MzIzMTk5LCJpYXQiOjE3MTU3MDEzMTQsIm93bmVyIjoiNjFjY2U2MmMxMjFjYTEwMDAxOGJiMTkwIiwic2NvcGUiOiJtdWx0aV90ZWFtLHJiYWMsZ3JhcGgsZmVkZXJhdGlvbiIsInYiOiIyIn0.uASWAZPQLDApZ6Ae1kY53hBVpivAxgz9dy5PMbnj1qUnH0Da1rlUOHFHV5LSWH4eu1a1uttlJAShafVUIrWewFesPJJHKZrv2aPBKE8Zt7z48ZMfI0ctq-DWyFB9JrMtBqgu0hkFYoHHjWhQzrWUgIuwypbSNYD_XrcKw1bwu6tcudxubttkH0I2j1JX5B3_hDQAKIFQ3gw8bIVqziUmpQR2nR3q2tZ2u8ZK6vrdIyn3PFl5715dYq4ZWw5Uad33L9NtTGzgFMMzcT-mhbpZX22lYmqsrJQNrQbIJW7p6K0cy-iWwO5py8ppkqnTfFbmDC691dZXNUx1eyCUQ5jmAQ
```

Install Tyk using ArgoCD Application CRDs

```
kubectl apply -f apps/tyk.yaml
```

You can expose the Tyk Gateway to your localhost using the following command:
```
kubectl port-forward svc/gateway-svc-tyk-gateway --namespace tyk 8080 &
```

You can check the state of the Tyk gateway using the following `curl` command:
```
curl localhost:8080/hello
```

### Tyk Operator

Install Tyk Operator using ArgoCD Application CRDs

```
kubectl apply -f apps/tyk-operator.yaml
```

### Deploy HttpBin service and expose it through the Tyk Gateway

Install Tyk Operator using ArgoCD Application CRDs
```
kubectl apply -f apps/httpbin.yaml
```

You can access the httpbin api using the following curl command:
```
curl localhost:8080/httpbin/get
```

### Keycloak

Install Keycloak using ArgoCD Application CRDs

```
kubectl apply -f apps/keycloak.yaml
```

You can expose Keycloak to your localhost using the following command:
```
kubectl port-forward svc/keycloak-service --namespace tyk 7000 &
```

You can access the Keycloak instance in your browser at [localhost:7000](http://localhost:7000):
```
Username: default@example.com
Password: topsecretpassword
```

### Expose HttpBin through the Tyk Gateway and manage AuthN and AuthZ using OAuth2.0

There are three user profiles available; you can generate a JWT associated 
with each profile using the following curl commands. The JWT can be passed to
the gateway under the `Authorization` header:

- Developer user, gives access to `/xml` endpoint:
```
curl -L --insecure -s -X POST 'http://localhost:7000/realms/keycloak-oauth/protocol/openid-connect/token' \
   -H 'Content-Type: application/x-www-form-urlencoded' \
   --data-urlencode 'client_id=keycloak-oauth' \
   --data-urlencode 'grant_type=password' \
   --data-urlencode 'client_secret=NoTgoLZpbrr5QvbNDIRIvmZOhe9wI0r0' \
   --data-urlencode 'scope=openid' \
   --data-urlencode 'username=developer@example.com' \
   --data-urlencode 'password=topsecretpassword' | jq -r '.access_token'
```

- Admin user, gives access to all endpoints:
```
curl -L --insecure -s -X POST 'http://localhost:7000/realms/keycloak-oauth/protocol/openid-connect/token' \
   -H 'Content-Type: application/x-www-form-urlencoded' \
   --data-urlencode 'client_id=keycloak-oauth' \
   --data-urlencode 'grant_type=password' \
   --data-urlencode 'client_secret=NoTgoLZpbrr5QvbNDIRIvmZOhe9wI0r0' \
   --data-urlencode 'scope=openid' \
   --data-urlencode 'username=admin@example.com' \
   --data-urlencode 'password=topsecretpassword' | jq -r '.access_token'
```

- Random user, does not give access even if it can generate a valid Keycloak JWT:
```
curl -L --insecure -s -X POST 'http://localhost:7000/realms/keycloak-oauth/protocol/openid-connect/token' \
   -H 'Content-Type: application/x-www-form-urlencoded' \
   --data-urlencode 'client_id=keycloak-oauth' \
   --data-urlencode 'grant_type=password' \
   --data-urlencode 'client_secret=NoTgoLZpbrr5QvbNDIRIvmZOhe9wI0r0' \
   --data-urlencode 'scope=openid' \
   --data-urlencode 'username=random@example.com' \
   --data-urlencode 'password=topsecretpassword' | jq -r '.access_token'
```

You can access the httpbin Keycloak managed api using the following curl command:
```
curl -L --insecure -s -X POST 'http://localhost:8080/httpbin-jwt/get' \
   -H 'Authorization: $JWT'
```