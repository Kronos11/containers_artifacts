# Challenge 3 - AKS with Security

## New AKS Cluster
```bash
az aks create \
 --resource-group teamResources \
 --name AKSSecurity8 \
 --network-plugin azure \
 --vnet-subnet-id /subscriptions/4b2d4309-4d3f-441e-82b8-9af621bfd8fe/resourceGroups/teamResources/providers/Microsoft.Network/virtualNetworks/vnet/subnets/aks-subnet \
 --docker-bridge-address 172.17.0.1/16 \
 --dns-service-ip 10.0.0.10 \
 --service-cidr 10.0.0.0/24 \
 --generate-ssh-keys \
 --enable-aad \
 --enable-azure-rbac


 az aks get-credentials --resource-group teamResources --name AKSSecurity8 --admin
 
kubectl create namespace api-namespace

kubectl create namespace web-namespace

vi role-api-namespace.yaml

kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: api-dev-writer
  namespace: api-namespace
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["all"]
---
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: api-dev-reader
  namespace: api-namespace
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["get","watch","list"]


kubectl apply -f role-api-namespace.yaml

vi role-web-namespace.yaml 

kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: web-dev-writer
  namespace: web-namespace
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["all"]
---
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: web-dev-reader
  namespace: web-namespace
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["get","watch","list"]

kubectl apply -f role-web-namespace.yaml

vi rolebinding-api-namespace.yaml

kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: api-user-access
  namespace: api-namespace
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: api-dev-writer
subjects:
- kind: User
  namespace: api-namespace
  name: 54045861-f87d-4eda-8a6a-e288ce9c2cfb
  
  kubectl apply -f rolebinding-api-namespace.yaml
  
  vi rolebinding-web-namespace.yaml
  
  kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: api-user-web-access
  namespace: web-namespace
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: web-dev-reader
subjects:
- kind: User
  namespace: web-namespace
  name: 54045861-f87d-4eda-8a6a-e288ce9c2cfb
  
  kubectl apply -f rolebinding-web-namespace.yaml
  
  vi rolebinding-web-user.yaml
  
  kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: web1-user-access
  namespace: web-namespace
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: api-dev-writer
subjects:
- kind: User
  namespace: api-namespace
  name: 43dc1b25-69c5-46ed-adec-b7f6f4fe1f4d
~                                             

kubectl apply -f rolebinding-web-user.yaml

vi rolebinding-web1-user.yaml

kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: api1-user-access
  namespace: api-namespace
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: web-dev-reader
subjects:
- kind: User
  namespace: api-namespace
  name: 54045861-f87d-4eda-8a6a-e288ce9c2cfb
~                                            

kubectl apply -f rolebinding-web1-user.yaml






