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

### Todo
0. explore why without hostname the job is there?
  a. explore TestCliConfigList  
1. make sense of airflow chart unittest
2. explore how to run python unittest in vscode
3. run it on airflow debugger


### Unittest using helm
```
import unittest

# Import the chart module
import chart

class TestChart(unittest.TestCase):
    def test_chart_template(self):
        # Render the chart template
        rendered_template = chart.render()
        
        # Verify that the template has the expected structure and content
        self.assertIn('metadata', rendered_template)
        self.assertIn('spec', rendered_template)
        self.assertIn('containers', rendered_template['spec'])
        
    def test_chart_install(self):
        # Install the chart
        chart.install()
        
        # Verify that the chart was installed successfully
        # (e.g. by checking for the presence of the chart's resources in the cluster)
        pass
        
    def test_chart_upgrade(self):
        # Upgrade the chart
        chart.upgrade()
        
        # Verify that the chart was upgraded successfully
        # (e.g. by checking for the presence of the upgraded chart's resources in the cluster)
        pass
        
    def test_chart_uninstall(self):
        # Uninstall the chart
        chart.uninstall()
        
        # Verify that the chart was uninstalled successfully
        # (e.g. by checking that the chart's resources are no longer present in the cluster)
        pass

if __name__ == '__main__':
    unittest.main()

``` 


### verify
```
kubectl get deployment airflow-dag-processor -n airflow -o yaml 
```


helm show values airflow-1.8.0-dev > values.yaml
helm upgrade --install airflow airflow-1.8.0-dev.tgz --namespace airflow --create-namespace --values values2.yaml


dag_processor_liveness_check_command

pyenv activate airflow-env
breeze start-airflow


pytest tests/utils/test_decorators.py
pytest tests/charts -n auto
breeze testing helm-tests  --python 3.8 
pytest tests/charts/test_basic_helm_chart.py
pytest tests/charts/test_basic_helm_chart.py -k test_basic_deployments --pdb

^^^^^^^^^^^
todo
remove --hostname
1. find how to check liveness probe
2. find how to check if dagsprocessor is working

standalone_dag_processor

1. understand lifycyle of scheduler in airflow

kubectl exec -n airflow -ti airflow-postgresql-0 -- /bin/bash 

select distinct hostname from job
where hostname like 'airflow%' 
and hostname not like 'airflow-scheduler%'
and hostname not like 'airflow-triggerer%';

NAME                                 READY   STATUS    RESTARTS   AGE
airflow-postgresql-0                 1/1     Running   0          9m59s
airflow-redis-0                      1/1     Running   0          9m59s
airflow-scheduler-6dc64c57d-z26cp    2/2     Running   0          9m59s
airflow-statsd-64cdcfb8f8-h7cg8      1/1     Running   0          9m59s
airflow-triggerer-784dff75b-bx9z4    1/1     Running   0          9m59s
airflow-webserver-85945999b9-2knf9   1/1     Running   0          9m59s
airflow-worker-0                     2/2     Running   0          9m59s


alternative:
    what indicate job is live? airflow jobs check only?
        airflow/cli/commands/scheduler_command.py _run_scheduler_job
    can we use airflow jobs check --hostname (hostname_scheduler name?)
        - simulate jobs not workin (check in test code)
        - check if most recent scheduler still live?

#### debug pytest
$ pyenv activate airflow-env
$ export AIRFLOW_HOME=/home/farhan/projects/airflow
$ pytest --collect-only

Not Stand Alone
    SchedulerJob >> BaseJob >> get_hostname()
    SchedulerJob._execute() >> DagFileProcessorAgent.start >> DagFileProcessorManager.start()
Stand Alone
    DagFileProcessorManager.start()
    Is not a job >> find command to check if processing is working fine
    _run_processor_manager
    TestDagProcessorCommand
    pytest tests/cli/commands/test_dag_processor_command.py --pdb
    mock_dag_manager.return_value.start.assert_called()
    Dag Processor runs as a standalone component. Users need to start a scheduler job (airflow scheduler) and Dag Processor (airflow dag-processor) independently.
    idea -> wrap the manager within a dag-processor-job? probably using BaseJob or create new type job
    example -> _run_scheduler_job
    /airflow/airflow/cli/commands/scheduler_command.py

Scheduler
    basejob.run()
        scheduler._execute()

select * from job where job_type='DagProcessorJob';
\c airflow
DagProcessorJob not have start_date?
 id | dag_id |  state  |    job_type     |          start_date           |           end_date            |       latest_heartbeat        | executor_class |   hostname   | unixname 
----+--------+---------+-----------------+-------------------------------+-------------------------------+-------------------------------+----------------+--------------+----------
  1 |        | success | DagProcessorJob |                               | 2023-01-06 07:08:32.292794+00 |                               |                |              | 
  3 |        | failed  | SchedulerJob    | 2023-01-06 07:19:52.036351+00 |                               | 2023-01-06 07:48:06.059109+00 | LocalExecutor  | 468b239cf0a6 | root
  5 |        | running | SchedulerJob    | 2023-01-06 08:02:18.636959+00 |                               | 2023-01-06 08:04:40.404723+00 | LocalExecutor  | d57ab1a66f16 | root
  6 |        | running | TriggererJob    | 2023-01-06 08:02:18.752535+00 |                               | 2023-01-06 08:04:40.749677+00 | LocalExecutor  | d57ab1a66f16 | root
  2 |        | running | TriggererJob    | 2023-01-06 07:00:40.616134+00 |                               | 2023-01-06 07:16:22.042025+00 | LocalExecutor  | 8f6e7ec7c2ba | root
  4 |        | success | DagProcessorJob |                               | 2023-01-06 07:39:34.219752+00 |                               |                |              | 