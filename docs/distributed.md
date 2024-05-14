# Tyk (Distributed deployment)

Make sure you have the Tyk Dashboard and MDCB license keys ready and replace "<REPLACE_WITH_DASH_LICENSE>" and "<REPLACE_WITH_MDCB_LICENSE>" with your actual keys in the command below.

First, install Tyk control plane.

Create secret with Tyk credentials:
```
kubectl create secret generic tyk-cp-conf --namespace=tyk \
   --from-literal=APISecret=CHANGEME \
   --from-literal=AdminSecret=12345 \
   --from-literal=DashLicense=<REPLACE_WITH_DASH_LICENSE> \
   --from-literal=MDCBLicense=<REPLACE_WITH_MDCB_LICENSE>
```

Install Tyk using ArgoCD Application CRDs

```
kubectl apply -f apps/tyk-control-plane.yaml
```

You can expose the Tyk Dashboard to your localhost using the following command:

```
kubectl port-forward svc/dashboard-svc-tyk-cp-tyk-dashboard --namespace tyk 3000:3000 &
```

You can access Tyk Dashboard here:
```
http://localhost:3000
```

Default admin login is "default@example.com" / "topsecretpassword". It can be changed in the ArgoCD app manifest.

Next, install the Tyk data plane.

Create secret with remote control plane credentials:
```
kubectl create secret generic tyk-dp-conf --namespace=tyk \
   --from-literal=APISecret=CHANGEME \
   --from-literal=AdminSecret=12345 \
   --from-literal=groupID=mygroup \
   --from-literal=orgId=$(kubectl get secret tyk-operator-conf --namespace=tyk -o jsonpath='{.data.TYK_ORG}' | base64 -d) \
   --from-literal=userApiKey=$(kubectl get secret tyk-operator-conf --namespace=tyk -o jsonpath='{.data.TYK_AUTH}' | base64 -d)
```

Install Tyk using ArgoCD Application CRDs

```
kubectl apply -f apps/tyk-data-plane.yaml
```

You can expose the Data Plane Tyk Gateway to your localhost using the following command:

```
kubectl port-forward svc/gateway-svc-tyk-dp-tyk-gateway --namespace tyk 8080:8080 &
```

You can check the state of the Tyk gateway using the following `curl` command:
```
curl localhost:8080/hello
```