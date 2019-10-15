
## Helm and Tiller installation

### 2. Create helm tiller namespace and service-account:
```
kubectl create ns kube-tiller
kubectl create serviceaccount tiller --namespace kube-tiller

cat << EOF | kubectl create -f -
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-tiller
EOF
```

### 3. Install Helm Chart package manager:
```
helm init --service-account tiller --tiller-namespace kube-tiller
```
