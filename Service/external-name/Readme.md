# Service ExternalName
**Service** with type *ExternalName* allows to connect pods with external server. Cluster pods can work with an external server as if it is a part of the cluster.
**Service** below will route all requests to server *dummy.restapiexample.com*.
```yaml
apiVersion: v1
kind: Service
metadata:
  name: test-svc
spec:
   type: ExternalName
   externalName: dummy.restapiexample.com
```
![](images/external-name.png)
Different as for another types of **Service**, this component does not create any cluster IP. Client pods have to use internal service name. In this case, it is *test-svc* or from another namespace *test-svc.default.svc.cluster.local*.
```
$ kubectl apply -f service.yaml
service/test-svc created

$ kubectl get svc
NAME         TYPE           CLUSTER-IP   EXTERNAL-IP                PORT(S)   AGE
kubernetes   ClusterIP      100.64.0.1   <none>                     443/TCP   7d21h
test-svc     ExternalName   <none>       dummy.restapiexample.com   <none>    12s
```
Now we can check if it works. To do it, we start a new pod in the same namespace, and ping the service. In response, we should get IP of the external server.
```
$ kubectl run curl --image=radial/busyboxplus:curl -i --tty --generator=run-pod/v1
If you don't see a command prompt, try pressing enter.
$ ping test-svc
PING test-svc (34.250.200.167): 56 data bytes
64 bytes from 34.250.200.167: seq=0 ttl=62 time=1.074 ms
64 bytes from 34.250.200.167: seq=1 ttl=62 time=0.608 ms
64 bytes from 34.250.200.167: seq=2 ttl=62 time=0.639 ms
64 bytes from 34.250.200.167: seq=3 ttl=62 time=0.721 ms
64 bytes from 34.250.200.167: seq=4 ttl=62 time=0.608 ms
```
Note that request was forwarded to the external server.
Please note, if you try to send HTTP request to the service,  you will get 403 error code, because this server checks the url and refuses to serve it if it does not correspond to the original server url. Anyway, the requests reaches the server:
```
$ curl -v test-svc

> GET / HTTP/1.1
> User-Agent: curl/7.35.0
> Host: test-svc
> Accept: */*
>
< HTTP/1.1 403 Forbidden
< Content-Type: text/plain
< Date: Sun, 24 Nov 2019 17:08:12 GMT
< Content-Length: 9
<
Forbidden
```
