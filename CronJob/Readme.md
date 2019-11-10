
Creates cron job that starts every minute. Only one job at a time is allowed.

```
$ kubectl apply -f cronjob.yaml
cronjob.batch/cron-job created
```

```
$ kubectl get pods
NAME                        READY   STATUS      RESTARTS   AGE
cron-job-1573398780-wkwgc   0/1     Completed   0          3m13s
cron-job-1573398840-rbpq5   0/1     Completed   0          2m13s
cron-job-1573398900-fp5bl   0/1     Completed   0          73s
cron-job-1573398960-4mhrh   1/1     Running     0          13s
```
