# Challenge 2 - Creating AKS

## Creating AKS Cluster
```bash
az aks create -n AKS-Team8 -g teamResources --attach-acr registryrca1339 
```
## Creating Deployments & Services

### Created Namespace
kind: Namespace
apiVersion: v1
metadata:
  name: team8
  
### Create secret for sqlserver
apiVersion: v1
data:
  username: <usernamebase64>
  password: <passbase64>
kind: Secret
metadata:
  name: sqlserver-secret
  namespace: team8
type: Opaque

### Create Deployments for Each Application

> Ensure environment variables passed in to containers

> create yml file for each application
```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: trips-deployment
  namespace: team8
  labels:
    app: trips
spec:
  replicas: 2
  selector:
    matchLabels:
      app: trips
  template:
    metadata:
      labels:
        app: trips
    spec:
      containers:
      - name: trips
        image: registryrca1339.azurecr.io/trips-jb:1.0
        ports:
        - containerPort: 80
        env:
        - name: SQL_USER
          valueFrom:
            secretKeyRef:
              name: sqlserver-secret
              key: username
        - name: SQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: sqlserver-secret
              key: password
        - name: SQL_SERVER
          value: sqlserverrca1339.database.windows.net
---
apiVersion: v1
kind: Service
metadata:
  name: trips-service
  namespace: team8
spec:
  type: LoadBalancer
  ports: 
  - name: http
    port: 80
    targetPort: 80
  selector:
    app: trips
```
  
 ### TripViewer Yml
  ```yml
  apiVersion: apps/v1
kind: Deployment
metadata:
  name: tripviewer-deployment
  namespace: team8
  labels:
    app: tripviewer
spec:
  replicas: 2
  selector:
    matchLabels:
      app: tripviewer
  template:
    metadata:
      labels:
        app: tripviewer
    spec:
      containers:
      - name: tripviewer
        image: registryrca1339.azurecr.io/tripviewer-jb:1.0
        ports:
        - containerPort: 80
        env:
        - name: TRIPS_API_ENDPOINT
          value: "http://trips-service"
        - name: USERPROFILE_API_ENDPOINT
          value: "http://userprofile-service"
--- 
apiVersion: v1
kind: Service
metadata:
  name: tripviewer-service
  namespace: team8
spec:
  type: LoadBalancer
  ports: 
  - name: http
    port: 80
    targetPort: 80
  selector:
    app: tripviewer
  ```
