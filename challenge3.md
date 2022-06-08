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
```


