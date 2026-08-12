Comando per fermare il cluster AKS

```coffee
az aks stop --name sdm-aks1 --resource-group sdm-aks-rg
```

Procedura per effettuare l’installazione di PX su AKS

Login ad AZ

```coffee
az login
```

si apre una pagina browser per l’autenticazione. Accettare. 

Per verificare la subscription id:

```coffee
az account list 
```

Impostare la subscription id

```coffee
az account set --subscription <Your-Azure-Subscription-UUID>
```

Verificare le location di Azure con il comando:

```coffee
az account list-locations
```

creare il resource-group-name nella rispettiva location

```coffee
az group create --name <resource-group-name> --location <location>
```

Procedere con la creazione del cluster AKS (via GUI o CLI)

Al termine della creazione, procedere con la creazione del custom role permission (già esistono su Azure)

```coffee
az role definition create --role-definition '{
"Name": "<your-role-name>",
"Description": "",
"AssignableScopes": [
    "/subscriptions/<your-subscription-id>"
],
"Actions": [
    "Microsoft.ContainerService/managedClusters/agentPools/read",
    "Microsoft.Compute/disks/delete",
    "Microsoft.Compute/disks/write",
    "Microsoft.Compute/disks/read",
    "Microsoft.Compute/virtualMachines/write",
    "Microsoft.Compute/virtualMachines/read",
    "Microsoft.Compute/virtualMachineScaleSets/virtualMachines/write",
    "Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read"
],
"NotActions": [],
"DataActions": [],
"NotDataActions": []
}'
```

Trovare l’infrastructure resource-group creato durante la creazione del cluster AKS mediante il comando:

```coffee
az aks show -n <aks-cluster-name> -g <aks-resource-group> | jq -r '.nodeResourceGroup'
```

Assegnare il custom-role all’infrastructure-resource-group appena trovato

```coffee
az ad sp create-for-rbac --role=<your-role-name> --scopes="/subscriptions/<your-subscription-id>/resourceGroups/<aks-infrastructure-resource-group>"
```

Dovrebbe esserci un output simile:

```coffee
{
  "appId": "xxxxxxxx-xxxx-xxxx-xxxx-1234567890ab",
  "displayName": "azure-cli-2020-10-10-10-10-10",
  "name": "http://azure-cli-2020-10-10-10-10-10",
  "password": "xxxxxxxx-xxxx-xxxx-xxxx-1234567890ab",
  "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-1234567890ab"
}
```

Procedere con la creazione delle secret per px-azure che vengono passate a Portworx tramite Azure APIs. 

Set AZURE\_TENANT\_ID to the value for tenant

Set AZURE\_CLIENT\_ID to the value for appId

Set AZURE\_CLIENT\_SECRET to the value for password

```coffee
kubectl create secret generic -n kube-system px-azure --from-literal=AZURE_TENANT_ID=<tenant> \
                                                      --from-literal=AZURE_CLIENT_ID=<appId> \
                                                      --from-literal=AZURE_CLIENT_SECRET=<password>
```