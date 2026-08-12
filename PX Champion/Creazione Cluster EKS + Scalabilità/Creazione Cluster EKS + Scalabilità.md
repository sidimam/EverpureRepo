Per poter creare un cluster EKS è necessario installare sul MacOS: eksctl + aws cli. Configurare aws con le access key e il file config per la region.

Posizionarsi nella directory nascosta .aws

```coffee
cd .aws
```

Creare un file yaml contenente la configurazione EKS che si desidera avere tipo es il file eksctl-sdm-eks1.yaml:

```coffee
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig

metadata:
  name: sdm-eks1
  region: eu-south-1

nodeGroups:
  - name: ng1
    instanceType: m5.xlarge
    desiredCapacity: 3
    ssh:
      publicKeyPath: ~/.ssh/sdm-pubkey.pub 
```

Verificare con un dryrun:

```coffee
eksctl create cluster -f eksctl-sdm-eks1.yaml --dry-run
```

Se ritorna con esito positivo il file yaml compilato allora procedere con la creazione del cluster:

```coffee
eksctl create cluster -f sdm-eks1.yaml 
```

Verificare se il cluster esiste

```coffee
eksctl get cluster
```

Verificare se la creazione è terminata con successo:

```coffee
eksctl get nodegroup --cluster sdm-eks1 --region eu-south-1 --name ng1
```

Quando non si utilizza la risorsa EKS scalarla a 0:

```coffee
eksctl scale nodegroup --cluster sdm-eks1 --name ng1 --nodes 0 --nodes-max 3 --nodes-min 0
```

Per riattivare la risorsa scalarla nuovamente a 3:

```coffee
eksctl scale nodegroup --cluster sdm-eks1 --name ng1 --nodes 3 --nodes-max 3 --nodes-min 0
```

Scaricare il file config per connettersi al cluster k8s

```coffee
aws eks update-kubeconfig --region eu-south-1 --name sdm-eks1 
```

Se si utilizzano più profili :--profile sdimambro-pure

