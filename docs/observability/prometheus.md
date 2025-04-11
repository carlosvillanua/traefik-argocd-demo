### Prometheus

You can expose Prometheus to your localhost using the following command:
```
kubectl port-forward svc/traefik-prometheus-server --namespace traefik 9090:80 &
```
