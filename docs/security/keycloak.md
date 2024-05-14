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