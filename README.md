#https://hub.docker.com/r/confluentinc/cp-zookeeper
#https://hub.docker.com/r/confluentinc/cp-kafka/
#https://hub.docker.com/r/confluentinc/cp-control-center
#https://hub.docker.com/r/hlebalbau/kafka-manager/

#https://www.nuget.org/packages/Confluent.Kafka/
#https://www.conduktor.io/
#https://medium.com/@srigumm/building-realtime-streaming-applications-using-net-core-and-kafka-ad45ed081b31
#https://docs.confluent.io/kafka-clients/dotnet/current/overview.html

#docker pull confluentinc/cp-zookeeper
#docker pull confluentinc/cp-kafka
#docker network create kafka
#docker run -d --network=kafka --name=zookeeper -e ZOOKEEPER_CLIENT_PORT=2181 -e ZOOKEEPER_TICK_TIME=2000 -p 2181:2181 confluentinc/cp-zookeeper 
#docker run -d --network=kafka --name=kafka -p 9092:9092 -e KAFKA_ZOOKEEPER_CONNECT=zookeeper:2181 -e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://localhost:9092 confluentinc/cp-kafka

#docker pull hlebalbau/kafka-manager
#docker run -d --network=kafka --name=kafka-manager -p 9000:9000 -e ZK_HOSTS="zookeeper:2181" hlebalbau/kafka-manager:latest

#-----------------------------------------------

version: '3'
services:
  zookeeper:
    image: wurstmeister/zookeeper
    container_name: zookeeper
    restart: always
    ports:
      - "2181:2181"
  kafka:
    image: wurstmeister/kafka
    container_name: kafka
    restart: always
    ports:
      - "9092:9092"
      - "1099:1099"
    environment:
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://localhost:9092
      KAFKA_LISTENERS: PLAINTEXT://0.0.0.0:9092
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_ADVERTISED_HOST_NAME: kafka
      JMX_PORT: 1099
  kafka_manager:
    image: hlebalbau/kafka-manager:stable
    restart: always
    ports:
      - "9000:9000"
    environment:
      ZK_HOSTS: zookeeper:2181
    command: -Dpidfile.path=/dev/null

#-----------------------------------------------------------
#docker-compose up --build -d
#-----------------------------------------------------------
docker exec -it zookeeper bash
./bin/zkCli.sh
create /kafka-manager/mutex ""
create /kafka-manager/mutex/locks ""
create /kafka-manager/mutex/leases ""
