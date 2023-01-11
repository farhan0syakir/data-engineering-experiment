## helm
helm repo add bitnami https://charts.bitnami.com/bitnami
helm show values bitnami/kafka > values.yaml
helm install my-release bitnami/kafka --namespace kafka

# docker compose
source https://github.com/provectus/kafka-ui/blob/master/documentation/compose/kafka-ui.yaml
