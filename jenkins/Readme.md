helm repo add jenkins https://charts.jenkins.io
helm show values jenkins/jenkins > values.yaml
helm upgrade --install jenkins jenkins/jenkins --namespace jenkins --create-namespace --values values.yaml

kubectl exec --namespace jenkins -it svc/jenkins -c jenkins -- /bin/cat /run/secrets/additional/chart-admin-password && echo