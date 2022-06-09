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

## Add identity Access
https://docs.microsoft.com/en-us/azure/aks/use-azure-ad-pod-identity
```azureCLI
az feature register --name EnablePodIdentityPreview --namespace Microsoft.ContainerService
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview

az aks update -g teamResources -n AKSSecurity8 --enable-pod-identity

# Use identity
az aks show \
-g teamResources \
-n AKSSecurity8 \
--query "addonProfiles.azureKeyvaultSecretsProvider.identity.clientId" -o tsv
Assign identity to Team8KeyVault

# set policy to access keys in your key vault
az keyvault set-policy -n Team8KeyVault --key-permissions get --spn <pod-identity-client-id>
# set policy to access secrets in your key vault
az keyvault set-policy -n Team8KeyVault --secret-permissions get --spn <pod-identity-client-id>
```
