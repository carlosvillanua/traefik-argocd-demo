### OPA

Adds an OPA rule to the dashboard API to enforce Keycloak JWT Authentication on OAS APIs with "keycloak" value for auth in pluginConfig.

```
{
  "x-tyk-api-gateway": {
    "middleware": {
      "global": {
        "pluginConfig": {
          "data": {
            "enabled": true,
              "value": {
                "auth": "keycloak",
                ...
              }
            }
          }
        },
        ...
      },
      ...
    },
    ...
  },
  ...
}
```

Rego rule applied:
```
patch_request[x] {
   request_permission[_] == "apis"
   request_intent == "write"
   input.request.body["x-tyk-api-gateway"].middleware.global.pluginConfig.data.value.auth == "keycloak"
   x := {
      "components": {
         "securitySchemes": {
            "jwtAuth": {
               "type": "http",
               "scheme": "bearer",
               "bearerFormat": "JWT"
            }
         } 
      },
      "security": [
         {
            "jwtAuth": []
         }
      ],
      "x-tyk-api-gateway": {
         "server": {
            "authentication": {
               "enabled": true,
               "securitySchemes": {
                  "jwtAuth": {
                     "header": {
                        "enabled": true,
                        "name": "Authorization"   
                     },
                     "identityBaseField": "sub",
                     "policyFieldName": "pol",
                     "enabled": true,
                     "defaultPolicies": [ "dHlrL3VzZXJz" ],
                     "signingMethod": "rsa", 
                     "source": "aHR0cDovL2tleWNsb2FrLXNlcnZpY2UudHlrLnN2Yzo3MDAwL3JlYWxtcy9rZXljbG9hay1vYXV0aC9wcm90b2NvbC9vcGVuaWQtY29ubmVjdC9jZXJ0cw=="
                  }
               }
            } 
         }
      }
   }
}
```