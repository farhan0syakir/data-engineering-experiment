
# Install helm from sources

To build an Apache Airflow Helm chart from source, you will need to have the following tools installed:

Git: to clone the Apache Airflow source code repository

Helm: to package the chart and manage its dependencies

Docker: to build the Docker images required by the chart

First, clone the Apache Airflow repository:

```
git clone https://github.com/apache/airflow.git
```
Navigate to the chart directory:
```
cd airflow/chart
```
Use Helm to build the chart:
```
helm package .
```
This will create a file called airflow-X.Y.Z.tgz, where X.Y.Z is the version of Apache Airflow.

Use Helm to install the chart:
```
helm install airflow-X.Y.Z.tgz
```
This will install the Apache Airflow chart in your Kubernetes cluster. You can use the helm list command to verify that the chart has been installed.

You can also customize the chart by modifying the values.yaml file in the chart directory. This file contains the default configuration values for the chart, and you can override these values by providing your own values file when installing the chart.

For example, to install the chart with a custom values file:

```
helm install airflow-X.Y.Z.tgz -f myvalues.yaml
```