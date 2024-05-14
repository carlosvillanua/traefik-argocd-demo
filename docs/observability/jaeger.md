### Jaeger

You can expose Jaeger to your localhost using the following command:
```
kubectl port-forward svc/tyk-jaeger-query --namespace tyk 16686 &
```
