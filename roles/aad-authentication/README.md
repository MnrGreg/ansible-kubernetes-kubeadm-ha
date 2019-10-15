
## Notes on using Azure AD as an authentication provider for Kubernetes Adminstration

https://github.com/kubernetes/client-go/tree/master/plugin/pkg/client/auth/azure?source=post_page---------------------------

`On the "Manage" / "Manifest" set groupMembershipClaims property to SecurityGroup`

Azure Tenant ID: XXXXX

AzureAD API Server SP: ts-container-platform-sp
appid: XXXXX

AzureAD User Auth SP: 
appid: XXXXX

### 1. Allow cluster firewall access to https://sts.windows.net from k8s nodes

### 2. Set up kubernetes API-server with Azure AD ts-container-platform-sp SP (Applied by Ansible)

Update /etc/kubernetes/manifests/kube-apiserver.yaml (Applied by Ansible)

```
--oidc-client-id=spn:XXXXX
--oidc-issuer-url=https://sts.windows.net/XXXXX/
--oidc-username-claim=upn
--oidc-groups-claim=groups
```

### 3. Set Azure Group access for the cluster

#### Admin NonProduction Cluster Role
```
cat << EOF | kubectl apply -f -
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: XXXXX:cluster-admin
subjects:
  - kind: Group
    name: XXXXX
    apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: cluster-admin
  apiGroup: rbac.authorization.k8s.io
EOF
```

#### User NonProduction Cluster Role
```
cat << EOF | kubectl apply -f -
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: XXXXX:edit
subjects:
  - kind: Group
    name: XXXXX
    apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: edit
  apiGroup: rbac.authorization.k8s.io
EOF
```



### 4. Update client side kubectl config with azure user and cluster configuration

```
kubectl config set-credentials azureuser@domain.local  \
--auth-provider=azure \
--auth-provider-arg=environment=AzurePublicCloud \
--auth-provider-arg=client-id=XXXXX \
--auth-provider-arg=tenant-id=XXXXX \
--auth-provider-arg=apiserver-id=XXXXX

kubectl config set-cluster cluster1-nonprod \
--server=https://k8s-api01.nonprod.domain.local:6443 \
--insecure-skip-tls-verify=true

kubectl config set-context cluster1-nonprod_azureuser@domain.local \
--cluster=cluster1-nonprod \
--user=azureuser@domain.local

kubectl config use-context cluster1-nonprod_azureuser@domain.local
```