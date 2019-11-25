# Kubernetes Service object
## Overview
[A Kubernetes Service is a resource you create to make a single, constant point of entry to a group of pods, providing the same service. Each Service has an IP address and port that never change while service exists][1]. A Service object plays a mediator role between client application and pods. Note that pods can be restarted any time, number of pods can be changed. It is a task of a Service object to handle such situations a route the traffic in the correct way. Moreover, some pods of your application can use Service objects to access another pods. In the picture below, Service 1 distribute traffic between pods Pod 1.1-Pod 1.3, Service 2, collects traffic from pods Pod 1.1-Pod 1.3 and distributes it between pods Pod 2.1-Pod 2.3

![Service](images/service.png)
## Pod selector and endpoints
How does a Service recognize the pods it should serve? There are two ways to define the pods:
- pod selector,
- manualy created Endpoints object.
An Endpoints object contains a list of endpoints: IP addresses and ports. There is a one-to-one relationship between an Endpoinds object and a  Service object. Service object is not functional without endpoints. Endpoints object can be created automatically from pod selector defined in the Service or manually.
Codesnippet below uses pod selector. Endpoints objects are created automatically by Kubernetes.
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-nodeport-svc
  labels:
    app: nginx
spec:
 type: NodePort                      # service type (exposing a port outside of the cluster)
 selector:
    app: nginx
 ports:
    - nodePort: 30692                # port exposed by service (in range 30000-32767) (masterIP:30692)
      port: 80                       # port exposed by service (clusterIP:80)
      protocol: TCP                  # network protocol
      targetPort: 80                 # port exposed by pods
```
```
$ kubectl apply -f service.yaml
service/nginx-nodeport-svc created

$ kubectl get svc
NAME                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes           ClusterIP   100.64.0.1      <none>        443/TCP        20h
nginx-nodeport-svc   NodePort    100.69.224.51   <none>        80:30692/TCP   15s

$ kubectl get endpoints
NAME                 ENDPOINTS                                                  AGE
kubernetes           172.20.41.26:443                                           20h
nginx-nodeport-svc   100.96.6.29:80,100.96.6.32:80,100.96.7.42:80 + 1 more...   24s
```
Manually created Endpoints can be useful if you neet to connect your cluster with an external server. Endpoints object shall have the same name as a corresponded Service. Here is an example of Endpoints manifiest:
```yaml
apiVersion: "v1"
kind: "Endpoints"
  metadata:
    name: "external-server" 
  subsets: 
    - addresses:
      - ip: "111.10.120.14" #The IP Address of the external web server
      ports:
      - port: 80 
        name: "http"
```
## Creation of a Service object
There are two possibility to create a Service object:
- kubectl expose command
- kubernetes manifest
### Create Service object with kubectl expose command
### Create Service object with Kubernetes manifest
#### Service affinity
#### Reference ports by names
#### Service wihout pod selector - manual endpoints
### Service discovering
#### Environmental variables
#### Cluster DNS
## Services for connection to external IP
- using ExternalName type
## Exposing cluster to external clients
- NodePort Service
- LoadBalancer Service
- Ingress objects
### Difference between LoadBalancer and Ingress
### External Traffic Policy

[1]: <https://www.manning.com/books/kubernetes-in-action> "Marko Lukša, Kubernetes in Acttion, Manning, 2017"
