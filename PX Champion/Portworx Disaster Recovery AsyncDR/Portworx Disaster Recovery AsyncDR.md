Preparazione del cluster Portworx

```coffee
kubectl edit stc <stc-name> -n <namespace>
```

Aggiungere:

apiVersion: core.libopenstorage.org/v1

kind: StorageCluster

metadata:

 annotations:

 portworx.io/service-type: "portworx-api:LoadBalancer”

Verificare che il servizio porworx-api sia in modalità LoadBalancer

```coffee
kubectl get svc -n portworx
```