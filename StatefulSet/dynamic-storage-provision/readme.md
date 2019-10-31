# Creation of StatfulSet with dynamic storage provision
Files:
- storage-class.yaml - Creates a storage class for the stateful set
- statefulset.yaml - Creates the stateful set
- service.yaml - Creates a headless service to provide endpoinds fo the stateful set

```
$ # create storage class
$ kubectl apply -f storage-class.yaml
storageclass.storage.k8s.io/www created

$ # create the stateful set
$ kubectl apply -f statefulset.yaml
statefulset.apps/nginx-set created

$ # create service
$ kubectl apply -f service.yaml
service/nginx-svc created
```
Check created cluster object:
```
$ # get information about available storage classes
$ kubectl get sc
NAME   PROVISIONER             AGE
www    kubernetes.io/aws-ebs   8s

$ # check available stateful sets
$ kubectl get sts
NAME        READY   AGE
nginx-set   3/3     3m40s

$ # check available pods
$ kubectl get pods
NAME          READY   STATUS    RESTARTS   AGE
nginx-set-0   1/1     Running   0          4m52s
nginx-set-1   1/1     Running   0          4m
nginx-set-2   1/1     Running   0          3m23s

$ # check available persistent volume claims
$ kubectl get pvc
NAME              STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
www-nginx-set-0   Bound    pvc-ef3d910b-fc22-11e9-a19c-0a7942f17bf8   1Gi        RWO            www            2m48s
www-nginx-set-1   Bound    pvc-00a64c98-fc23-11e9-a19c-0a7942f17bf8   1Gi        RWO            www            2m19s
www-nginx-set-2   Bound    pvc-1719af3d-fc23-11e9-a19c-0a7942f17bf8   1Gi        RWO            www            101s

$ # check allocated persistent volumes
$ kubectl get pv | awk {'print $1,$2,$3,$4,$6'} | column -t
NAME                                      CAPACITY  ACCESS  MODES   POLICY
pvc-00a64c98-fc23-11e9-a19c-0a7942f17bf8  1Gi       RWO     Delete  default/www-nginx-set-1
pvc-1719af3d-fc23-11e9-a19c-0a7942f17bf8  1Gi       RWO     Delete  default/www-nginx-set-2
pvc-ef3d910b-fc22-11e9-a19c-0a7942f17bf8  1Gi       RWO     Delete  default/www-nginx-set-0

$ # get information about available services
$ kubectl get svc
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   100.64.0.1   <none>        443/TCP   82s
nginx-svc    ClusterIP   None         <none>        80/TCP    75s
```
