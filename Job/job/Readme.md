# Simple job
The code snippet in the job.yaml creates a single job. The job sleeps for 1 minute and exits.
```sh
$ kubectl apply -f job.yaml
job.batch/sleep created

$ kubectl get pods
NAME          READY   STATUS    RESTARTS   AGE
sleep-6ndtm   1/1     Running   0          10s

$ kubectl get pods
NAME          READY   STATUS      RESTARTS   AGE
sleep-6ndtm   0/1     Completed   0          68s
```
