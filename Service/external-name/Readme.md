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
Different as for another types of **Service**, this component does not create any cluster IP. Client pods have to use internal service name.
