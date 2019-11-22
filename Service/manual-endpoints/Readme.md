# Service with manual endpoints
If in a manifest of a **Service** object does not contain pod selector, endpoints for such a **Service** won't be created automatically. In this case, endpoints should be created manually. The **Endpoints** object should contain a list of IP addresses and ports that will be served by the service. Name of the **Endpoints** object shall be the same as name of the corresponded **Service** object.
In this example, **Service** object will forward messages to 3 nginx pods from different namespace.
First we create a new namespace with name *test*: 
```
$ kubectl create ns test
namespace/test created
```
Create a deployment with 3 nginx pods and display ip addresses of the pods:
```
$ kubectl apply -f deployment.yaml -n test
deployment.apps/nginx-dpl created

$ kubectl get pods -n test -o wide | awk {'print $1 " " $2 " " $3 " " $6'} | column -t
NAME                        READY  STATUS   IP
nginx-dpl-7d6b8c8944-ck58p  1/1    Running  100.96.6.47
nginx-dpl-7d6b8c8944-rjhkd  1/1    Running  100.96.6.48
nginx-dpl-7d6b8c8944-rjkjr  1/1    Running  100.96.8.58
```
Create a manifest with **Service** and **Endpoints** objects:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: dummy-svc
  labels:
    app: nginx
spec:
 ports:
    - protocol: TCP
      port: 80
      targetPort: 80
---
apiVersion: v1
kind: Endpoints
metadata:
  name: dummy-svc 
subsets: 
  - addresses:
    - ip: 100.96.6.47
    - ip: 100.96.6.48
    - ip: 100.96.8.58
    ports:
    - port: 80
```
Create the service:
```
$ kubectl apply -f service.yaml
service/dummy-svc created
endpoints/dummy-svc created

$ kubectl get svc
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
dummy-svc    ClusterIP   100.68.123.239   <none>        80/TCP    3m21s
kubernetes   ClusterIP   100.64.0.1       <none>        443/TCP   5d21h
```
To check how it works we start a new pod in the default namespace and query the service:
```
kubectl run curl --image=radial/busyboxplus:curl -i --tty --generator=run-pod/v1
If you don't see a command prompt, try pressing enter.
$ curl 100.68.123.239
<!DOCTYPE html>
<html>
<head><title>Welcome to nginx!</title>
...
```

                                                                                                                                         
