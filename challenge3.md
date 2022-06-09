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
#  Merged "AKSSecurity8-admin" as current context in /Users/jaspreetbhatia/.kube/config
 
# Create the API namespace
kubectl create namespace api-namespace

# Create the Web namespace 
kubectl create namespace web-namespace

# Create the Roles for Write and Read permissions within API namespace

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

# Apply the Roles to K8S 

kubectl apply -f role-api-namespace.yaml

# Create the Roles for Write and Read permissions within WEB namespace

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

# Apply the Roles to K8S 

kubectl apply -f role-web-namespace.yaml

# Create a YAML file for binding the Writer Role within API namespace to api-dev AAD user 

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
  
  # apply the Role binding to K8S 
  
  kubectl apply -f rolebinding-api-namespace.yaml
  
  # Create a YAML file for binding the Reader  Role within WEB namespace to api-dev AAD user 
  
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
 
  # apply the Role binding to K8S
  
  kubectl apply -f rolebinding-web-namespace.yaml
 
 # Create a YAML file for binding the Writer  Role within WEB namespace to web-dev AAD user
 
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
# apply the Role binding to K8S

kubectl apply -f rolebinding-web-user.yaml

 
 # Create a YAML file for binding the Reader  Role within API namespace to web-dev AAD user
 
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

# apply the Role binding to K8S

kubectl apply -f rolebinding-web1-user.yaml






