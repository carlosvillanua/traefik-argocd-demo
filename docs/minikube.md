# Minikube

Start Minikube
```
minikube start
minikube addons enable ingress
```

# ArgoCD

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