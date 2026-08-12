Una volta terminata la creazione cluster EKS AWS, andare sul sito Porworx Central per la generazione dello yaml necessario per installare il cluster PX.

Attraverso il wizard: 

![](resources/BCC4960C6EA1AB9B7133E2BF141C07D3.png)

cliccare su CUSTOMIZE:

![](resources/109141BCA51FA0DB9E22CEE809ACFA49.png)

cliccare NEXT: 

![](resources/3B9B8508C7A28F79147A85BB2B505C4C.png)

lasciare tutto in defaults (anche i drive GP3 proposti). cliccare NEXT:

![](resources/794E819B8D1EA9F23A1AA0B443248F5B.png)

cliccare NEXT:

![](resources/43D638AD5ADBEB45073A65460DF0B5ED.png)

Infine cliccare su FINISH

scaricare il file YAML cliccando sul Download .yaml

![](resources/2A06ACADEBA13AC3AFD2758A7B1E0FC6.png)

Procedere su EKS con la creazione del namespace “portworx”:

```coffee
kubectl create namespace portworx
```

Installare l’operator Portworx

```coffee
kubectl apply -f 'https://install.portworx.com/3.1?comp=pxoperator&kbver=1.30.5&ns=portworx'
```

[](https://install.portworx.com/3.1?comp=pxoperator&kbver=1.30.5&ns=portworx)editare il file .yaml scaricato e modificare la quantità di dischi GP3 richiesti (da 4 a 1), vedi esempio:

\# SOURCE: https://install.portworx.com/?operator=true&mc=false&kbver=1.30.5&ns=portworx&b=true&iop=6&s=%22type%3Dgp3%2Csize%3D50%22%2C%22type%3Dgp3%2Csize%3D50%22%2C%22type%3Dgp3%2Csize%3D50%22%2C%22type%3Dgp3%2Csize%3D50%22&ce=aws&c=px-cluster-26393b94-045c-4e7f-8338-866669c653e6&eks=true&stork=true&csi=true&mon=true&tel=true&st=k8s&promop=true

kind: StorageCluster

apiVersion: core.libopenstorage.org/v1

metadata:

 name: px-cluster-26393b94-045c-4e7f-8338-866669c653e6

 namespace: portworx

 annotations:

 portworx.io/install-source: "https://install.portworx.com/?operator=true&mc=false&kbver=1.30.5&ns=portworx&b=true&iop=6&s=%22type%3Dgp3%2Csize%3D50%22%2C%22type%3Dgp3%2Csize%3D50%22%2C%22type%3Dgp3%2Csize%3D50%22%2C%22type%3Dgp3%2Csize%3D50%22&ce=aws&c=px-cluster-26393b94-045c-4e7f-8338-866669c653e6&eks=true&stork=true&csi=true&mon=true&tel=true&st=k8s&promop=true"

 portworx.io/is-eks: "true"

spec:

 image: portworx/oci-monitor:3.1.6

 imagePullPolicy: Always

 kvdb:

 internal: true

 cloudStorage:

** deviceSpecs:**

** - type=gp3,size=50**

 secretsProvider: k8s

 stork:

 enabled: true

 args:

 webhook-controller: "true"

 autopilot:

 enabled: true

 runtimeOptions:

 default-io-profile: "6"

 csi:

 enabled: true

 monitoring:

 telemetry:

 enabled: true

 prometheus:

 enabled: true

 exportMetrics: true

 env:

Andare sulla console AWS e collegare la IAM Policy "EC2-Portworx-Clouddrive” al NodeInstanceRole creato durante la creazione con eksctl. 

![](resources/7D899209D26F97BED92302908306EC79.png)

cliccare su Add Permissions ed aggiungere la policy “EC2-Portworx-Clouddrive”:

![](resources/3B9752A95E86640C48B55E5CEE753115.png)

creare lo storage cluster PX:

```coffee
kubectl apply -f nomefile.yaml
```

Attendere l’aggiunta del disco GP3 da 50GB (e quello da 32GB per KVDB) alle istanze EC2 create con EKS. Verificare dalla console AWS se vengono creati i dischi: 

![](resources/2BF5B08FE46227D6FB9381E658BBF9FC.png)

Verificare lo status di installazione di PX:

```coffee
kubectl get all -n portworx 
kubectl -n portworx get storagenodes -l name=portworx
```

Verificare se i nodi siano online:![](resources/9FD703A926BC3D15E7BF70012AC7BA4B.png)

editare la configurazione dello storage cluster:

```coffee
kubectl get storagecluster -n portworx
```

![](resources/A4DEE62E14219D7860861A58AA35CA80.png)

```coffee
kubectl edit storagecluster "nome dello storage cluster" -n portworx 
```

Aggiungere al file di configurazione le seguenti righe sotto "spec” : 

 deleteStrategy:

 type: UninstallAndWipe 

salvare ed uscire (:wq!)

Verificare se tutti i pods sono in esecuzione:

```coffee
kubectl get pods -n portworx -o wide | grep -e portworx -e px
```