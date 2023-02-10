# Spring cloud data flow

## todo
1. deploy using helm
2. deploy stream

wget -O docker-compose.yml https://raw.githubusercontent.com/spring-cloud/spring-cloud-dataflow/main/src/docker-compose/docker-compose.yml;
wget -O docker-compose-kafka.yml https://raw.githubusercontent.com/spring-cloud/spring-cloud-dataflow/main/src/docker-compose/docker-compose-kafka.yml;
wget -O docker-compose-postgres.yml https://raw.githubusercontent.com/spring-cloud/spring-cloud-dataflow/main/src/docker-compose/docker-compose-postgres.yml;


export DATAFLOW_VERSION=2.10.0
export SKIPPER_VERSION=2.9.0
docker-compose -f docker-compose.yml -f docker-compose-kafka.yml -f docker-compose-postgres.yml up