# Running Docker SQL Server

## Create Common Docker Network
```bash
docker network create openhack 
```

## Run SQLServer locally
```bash
docker run --network openhack -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=<Password Here>" \
   -p 1433:1433 --name sql1 --hostname sql1 \
   mcr.microsoft.com/mssql/server:2017-latest
```

### Create mydrivingDB
```bash
docker exec -it sql1 "/opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P <Password Here>"
CREATE DATABASE mydrivingDB
```

## Login to Azure CR
Login with your hacker creds
```bash
az login
az acr login --name registryrca1339
```

## Load SQL Data
```bash
docker run --network openhack  -e SQLFQDN=sql1 -e SQLUSER=sa -e SQLPASS=<Password Here> -e SQLDB=mydrivingDB registryrca1339.azurecr.io/dataload:1.0
```

## Run POI

Copy Dockerfile_3 to src/poi
rename to Dockerfile


## Build Docker image for POI
```bash
docker build . -t poi
```

## Running POI
Update poi Dockerfile
> I had to add zscaler certificate to get dotnet restore to work

```bash
docker run --network openhack -p 8080:80 -e SQL_USER=sa -e SQL_PASSWORD=<Password Here> -e ASPNETCORE_ENVIRONMENT=Local poi
```
