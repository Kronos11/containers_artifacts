# Challenge 4

Enable Azure KeyVault Secrets Provider
```bash
az aks enable-addons --addons azure-keyvault-secrets-provider --name AKSSecurity8 --resource-group teamResources
```

Create Azure KeyVault
```bash
az keyvault create -n Team8KeyVault -g teamResources -l centralus
```

Add Secret
```bash
az keyvault secret set --vault-name Team8KeyVault -n SqlServerUser --value MyAKSExampleSecret
```
