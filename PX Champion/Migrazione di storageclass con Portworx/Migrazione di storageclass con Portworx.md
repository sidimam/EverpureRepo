1. Valutazione dei PVC esistenti:

• Identifica i PVC esistenti associati alla vecchia StorageClass e pianifica la loro migrazione. Puoi usare kubectl get pvc per visualizzare i dettagli.

```coffee
kubectl get pvc --namespace tuo-namespace
```

2\. Creazione della nuova StorageClass:

 • Crea una nuova StorageClass con il driver di Portworx (pxd.portworx.com) e specifica le impostazioni desiderate, come il tipo di replica, la policy di compressione, ecc.

YAML:

```coffee
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: nuova-portworx-sc
provisioner: pxd.portworx.com
parameters:
  repl: "3"  # Configura il numero di repliche secondo le esigenze
  io_profile: "db"  # Profilo IO ottimizzato, se necessario
```

3\. Snapshot del PVC originale:

 • Usa Portworx per creare uno snapshot del PVC esistente. Gli snapshot sono istantanei e rappresentano un modo efficace per trasferire i dati in modo sicuro.

YAML:

```coffee
apiVersion: volumesnapshot.external-storage.k8s.io/v1
kind: VolumeSnapshot
metadata:
  name: snapshot-pvc-originale
spec:
  persistentVolumeClaimName: pvc-originale
```

4\. Creazione di un nuovo PVC basato sullo snapshot:

 • Una volta creato lo snapshot, puoi creare un nuovo PVC che utilizza la nuova StorageClass e i dati dello snapshot.

```coffee
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: nuovo-pvc
spec:
  storageClassName: nuova-portworx-sc
  dataSource:
    name: snapshot-pvc-originale
    kind: VolumeSnapshot
    apiGroup: volumesnapshot.external-storage.k8s.io
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi  # Stessa dimensione o maggiore del PVC originale
```

5\. Aggiornamento delle applicazioni:

 • Modifica i deployment, gli statefulset o altri manifest di applicazioni per usare il nuovo PVC.

```coffee
apiVersion: apps/v1
kind: Deployment
metadata:
  name: applicazione
spec:
  template:
    spec:
      containers:
      - name: app-container
        volumeMounts:
        - mountPath: /path/nella/app
          name: nuovo-volume
      volumes:
      - name: nuovo-volume
        persistentVolumeClaim:
          claimName: nuovo-pvc
```

6\. Verifica della migrazione:

 • Controlla che l’applicazione stia leggendo e scrivendo correttamente dal nuovo PVC e che i dati siano stati migrati con successo.

 7\. Pulizia:

 • Dopo aver verificato che la migrazione sia avvenuta con successo e che il nuovo PVC stia funzionando come previsto, puoi eliminare il PVC originale e lo snapshot per liberare spazio.

```coffee
kubectl delete pvc pvc-originale --namespace tuo-namespace
kubectl delete volumesnapshot snapshot-pvc-originale --namespace tuo-namespace
```

