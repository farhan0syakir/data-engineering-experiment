# Install Airflow via Helm in Windows wsl

### Motivation
There is couple of reason why you want to deploy airflow in windows using helm 
- you want to learn about airflow or helm but you have windows laptop
- you want to fix bug in airflow repo about helm but you have windows laptop

### Steps
1. get docker desktop
2. Install docker and kubernetes in docker desktop
3. Run these commands

```
helm repo add apache-airflow https://airflow.apache.org
helm show values apache-airflow/airflow > values.yaml
helm upgrade --install airflow apache-airflow/airflow --namespace airflow --create-namespace --values values.yaml
```
4. edit some values.yaml
5. verify it exists
```
helm list --all-namespaces
```
6. check the dashboard
```
kubectl port-forward svc/airflow-webserver 8080:8080 --namespace airflow
```
then open localhost:8080


### Part 2 Extending Helm
There are a few different ways to add DAGs (Directed Acyclic Graphs) to an Apache Airflow installation managed by Helm:

Mount a volume containing your DAGs:
One option is to mount a volume containing your DAGs to the Airflow web server pod. You can do this by specifying a volumeMounts section in the airflow.webserver.resources.limits.cpu section of the values.yaml file. For example:

```
airflow:
  webserver:
    resources:
      limits:
        cpu: "1"
        memory: "1Gi"
      requests:
        cpu: "100m"
        memory: "128Mi"
    volumeMounts:
      - name: dags
        mountPath: /usr/local/airflow/dags

```
You will also need to specify a volumes section to define the volume itself. For example:


```
airflow:
  webserver:
    volumes:
      - name: dags
        hostPath:
          path: /path/to/dags
```
This will mount the host directory /path/to/dags to the /usr/local/airflow/dags directory in the Airflow web server pod, allowing you to access your DAGs from the Airflow web UI.

Use Git Sync:
Another option is to use the Git Sync container to synchronize your DAGs with a Git repository. To use this approach, you will need to enable the Git Sync feature in the values.yaml file:


```
airflow:
  gitSync:
    enabled: true
```
You will also need to specify the URL of the Git repository containing your DAGs and any necessary authentication information:


```
airflow:
  gitSync:
    repository: https://github.com/myuser/myrepo.git
    branch: master
    secrets:
      - apiKey
```
This will synchronize the DAGs in the specified Git repository with the /usr/local/airflow/dags directory in the Airflow web server pod, allowing you to access your DAGs from the Airflow web UI.

Use a ConfigMap:
Another option is to use a ConfigMap to store your DAGs and mount it to the Airflow web server pod. To do this, you will need to create a ConfigMap containing your DAGs and then mount it to the Airflow web server pod using the volumeMounts and volumes sections in the values.yaml file, similar to the approach described in the first method.

For example:


```
airflow:
  webserver:
    resources:
      limits:
        cpu: "1"
        memory: "1Gi"
      requests:
        cpu: "100m"
        memory: "128Mi"
    volumeMounts:
      - name: dags
        mountPath: /usr/local/airflow/dags
    volumes:
      - name: dags
        configMap:
          name: my-dags-configmap
```
This will mount the ConfigMap containing your DAGs to the /usr/local/airflow/dags directory in the Airflow web


```
kubectl create secret generic airflow-ssh-git-secret --from-file=gitSshKey=/home/farhan/.ssh/id_rsa -n airflow
```


### Uninstall
```
helm uninstall airflow -n airflow
```
