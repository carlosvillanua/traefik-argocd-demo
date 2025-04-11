### Tempo

You can expose Jaeger UI to your localhost using the following command:
```
kubectl port-forward svc/traefik-tempo --namespace traefik 16686 &
```

You can also use Grafana to view the traces. 
