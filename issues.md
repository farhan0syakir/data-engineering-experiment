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