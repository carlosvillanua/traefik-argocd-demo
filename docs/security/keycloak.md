### Keycloak

You can expose Keycloak to your localhost using the following command:
```
kubectl port-forward svc/keycloak-service --namespace tyk 7000 &
```

You can access the Keycloak instance in your browser at [localhost:7000](http://localhost:7000):
```
Username: default@example.com
Password: topsecretpassword
```
