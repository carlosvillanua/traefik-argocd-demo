### Tempo

You can expose Jaeger UI to your localhost using the following command:
```
kubectl port-forward svc/tyk-tempo --namespace tyk 16686 &
```

You can also use Grafana to view the traces. 
