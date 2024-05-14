### Grafana

You can expose Grafana to your localhost using the following command:
```
kubectl port-forward svc/tyk-grafana --namespace tyk 3001:80 &
```

Default admin login is "admin" / "topsecretpassword"