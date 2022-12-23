after change the values
```
  dagProcessor:
    enabled: true
```

```
kubectl get pod -n airflow
NAME                                     READY   STATUS    RESTARTS        AGE
airflow-dag-processor-74967685f7-xwssw   2/2     Running   1 (34s ago)     5m37s
airflow-postgresql-0                     1/1     Running   3 (8m16s ago)   3d2h
airflow-redis-0                          1/1     Running   3 (8m16s ago)   3d2h
airflow-scheduler-796b76dcfb-tpzd5       2/2     Running   0               5m37s
airflow-statsd-64cdcfb8f8-kvrnz          1/1     Running   3 (8m16s ago)   3d2h
airflow-triggerer-86d76c96f-4rdcd        2/2     Running   0               5m37s
airflow-webserver-79d54575dc-tgdpc       1/1     Running   0               5m37s
airflow-worker-0                         3/3     Running   0               5m32s
```


kubectl exec -n airflow --stdin --tty airflow-dag-processor-74967685f7-xwssw -- /bin/bash 
airflow jobs check --hostname $(hostname)

(airflow)airflow jobs check --hostname $(hostname)
No alive jobs found.