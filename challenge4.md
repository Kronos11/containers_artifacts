# Challenge 4

Enable Azure KeyVault Secrets Provider
```azureCLI
az aks enable-addons --addons azure-keyvault-secrets-provider --name AKSSecurity8 --resource-group teamResources
```

Create Azure KeyVault
```azureCLI
az keyvault create -n Team8KeyVault -g teamResources -l centralus
```

Add Secret
```azureCLI
az keyvault secret set --vault-name Team8KeyVault -n SqlServerUser --value <User>
az keyvault secret set --vault-name Team8KeyVault -n SqlServerPass --value <Password>
```


