# Setting Up Kubernetes Dashboard

## Deploy the Dashboard UI

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
```

## Accessing the Dashboard UI

```bash
kubectl proxy
```

## Use the below URL to access the UI

```bash
http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
```

## Create a user to login to the UI

#### dashboard-adminuser.yaml — Create a Service Account

```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
```

#### dashboard-clusterrole.yaml — Create a ClusterRoleBinding

```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: admin-user
    namespace: kubernetes-dashboard
```

#### dashboard-secret.yaml — long-lived Bearer Token for ServiceAccount

```
apiVersion: v1
kind: Secret
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
  annotations:
    kubernetes.io/service-account.name: "admin-user"
type: kubernetes.io/service-account-token
```

```bash
kubectl apply -f dashboard-adminuser.yaml -n kubernetes-dashboard
kubectl apply -f dashboard-clusterrole.yaml -n kubernetes-dashboard
kubectl apply -f dashboard-secret.yaml -n kubernetes-dashboard
```

#### Execute the following command to get the token which saved in the Secret:

```bash
kubectl get secret admin-user -n kubernetes-dashboard -o jsonpath={".data.token"} | base64 -d
```

#### Copy-paste the token in the UI and sign-in
