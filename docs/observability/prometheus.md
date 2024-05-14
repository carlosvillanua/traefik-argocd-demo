### Prometheus

You can expose Prometheus to your localhost using the following command:
```
kubectl port-forward svc/tyk-prometheus-server --namespace tyk 9090:80 &
```
