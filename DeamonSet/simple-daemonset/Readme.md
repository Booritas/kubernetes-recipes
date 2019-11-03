# Create a simple daemon for each node in the cluster
The code in the daemon-set.yaml create a pod that will run on every node in the cluster. The pod sleeps for an hour and exits. Upon pod termination, Kubernetes will start a new pod on the same node.
```sh
$ kubectl apply -f daemon-set.yaml
daemonset.apps/my-daemon-set created

$ kubectl get pods
NAME                  READY   STATUS      RESTARTS   AGE
my-daemon-set-hd56t   1/1     Running     0          19s
my-daemon-set-r6dcs   1/1     Running     0          19s
my-daemon-set-ttjkv   1/1     Running     0          19s

$ kubectl delete pod/my-daemon-set-hd56t
pod "my-daemon-set-hd56t" deleted

$ kubectl get pods
NAME                  READY   STATUS              RESTARTS   AGE
my-daemon-set-r6dcs   1/1     Running             0          93s
my-daemon-set-ttjkv   1/1     Running             0          93s
my-daemon-set-z55dq   0/1     ContainerCreating   0          0s

```
