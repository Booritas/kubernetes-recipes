# Creation of StatfulSet with dynamic storage provision
The files it this folder create a stateful set of pods. Dynamic storage provisioning in AWS is is used for persistence.
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
$ kubectl get pvc | awk {'print $1,$2,$3,$4,$5,$6'} | column -t
NAME             STATUS  VOLUME                                    CAPACITY  ACCESS  MODES
www-nginx-set-0  Bound   pvc-ef3d910b-fc22-11e9-a19c-0a7942f17bf8  1Gi       RWO     www
www-nginx-set-1  Bound   pvc-00a64c98-fc23-11e9-a19c-0a7942f17bf8  1Gi       RWO     www
www-nginx-set-2  Bound   pvc-1719af3d-fc23-11e9-a19c-0a7942f17bf8  1Gi       RWO     www

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
Check if one pod is reachable from another. For this purpose we do the following:
- login to the first pod
- install curl
- send a http request to the service
- send a http request directly to the third pod
```
$ # login to the bash of the firs pod (nginx-set-0)
$ kubectl -it exec nginx-set-0 bash
root@nginx-set-0:/#
$ # install curl
root@nginx-set-0:/# apt update
...
root@nginx-set-0:/# apt install curl
...

# send a http request to the service
root@nginx-set-0:/# curl nginx-svc
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title
...

# send a http request directly to the third pod 
root@nginx-set-0:/# curl nginx-set-2.nginx-svc
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title
```
To check persistance of the attached volumes we do following:
- create a text file in the first pod
- delete the first pod
- wait until the pod is recreated
- login to the pod and check the file
```
$ # login to the bash of the firs pod (nginx-set-0)
$ kubectl -it exec nginx-set-0 bash
root@nginx-set-0:/#

$ # create a text file
root@nginx-set-0:/# cat << EOF > /var/www/data/text.txt
This is the file
EOF

# exit the pod
root@nginx-set-0:/# exit
$

# delete the first pod
$ kubectl delete pod/nginx-set-0
pod "nginx-set-0" deleted

# wait until pod is recreated
$ kubectl get pod
NAME          READY   STATUS    RESTARTS   AGE
nginx-set-0   1/1     Running   0          50s
nginx-set-1   1/1     Running   0          50m
nginx-set-2   1/1     Running   0          49m

# login to the first pod and check the file
$ kubectl -it exec nginx-set-0 bash
root@nginx-set-0:/# cat /var/www/data/text.txt
This is the file
```
