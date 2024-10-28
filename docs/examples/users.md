### Users

Deploys 1 OAS API:
- users at /users

#### Expose Users API and manage AuthN using OAuth2.0

There are three user profiles available; you can generate a JWT associated
with each profile using the following curl command. To switch between profiles,
update the username with the email of the profile you like to use. The JWT can 
be passed to the gateway under the `Authorization` header:
- adming@example.com
- developer@example.com
- random@example.com

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

You can access the httpbin Keycloak managed api using the following curl command:
```
curl -L --insecure -s -X POST 'http://localhost:8080/users/get' \
   -H 'Authorization: $JWT'
```