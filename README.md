# Deploy Kafka 3.0
Deploy kafka 3.0 with or without Zookeeper. the container is able to run into 3 modes, both=kafka+zookeeper, kafka only or zookeeper only

## Assumptions
Host server: 192.168.0.158, 192.168.0.178
Client Port: 2181, 2182 and 2183
Kafka Port : 9091, 9092, 9093 and so on

## Dockerfile
The docker base image is from Alpine linux so it is quite small (around 300Mb). i dont use too many RUN commands, therefore there will be too many docker layers either

## How to build docker image
Get a dockerfile from this repo, let say you download it into /opt

<pre>
cd /opt
docker build --tag kafka -f ./Dockerfile-kafka-alpine /tmp
</pre>

## How to use the image

### Scenario 1
3 zookepper servers+kafka servers and 1 kafka server 

<pre>

docker run -it --name kafka1 -p 9091:9091 -p 2181:2181 -p 2999-3000:2999-3000 -e ZOOKEEPER_ID=1 \
-e KAFKA_PORT=9091 -e KAFKA_HOST_IP=$(hostname -i) \
-e ZOOKEEPER_SERVER1="192.168.0.178:2999:3000" \
-e ZOOKEEPER_SERVER2="192.168.0.158:3001:3002" \
-e ZOOKEEPER_SERVER3="192.168.0.158:3003:3004" \
-e ZOOKEEPER_CLIENT_PORT1=2181 \
-e ZOOKEEPER_CLIENT_PORT2=2182 \
-e ZOOKEEPER_CLIENT_PORT3=2183 \
-e KAFKA_INIT_CONFIG=yes -v /opt/kafka1:/opt/kafkadata kafka


docker run -it --name kafka2 -p 9092:9092 -p 2182:2182 -p 3001-3002:3001-3002 -e ZOOKEEPER_ID=2 \
-e KAFKA_BROKER_ID=1 -e KAFKA_PORT=9092 -e KAFKA_HOST_IP=$(hostname -i) \
-e ZOOKEEPER_SERVER1="192.168.0.178:2999:3000" \
-e ZOOKEEPER_SERVER2="192.168.0.158:3001:3002" \
-e ZOOKEEPER_SERVER3="192.168.0.158:3003:3004" \
-e ZOOKEEPER_CLIENT_PORT1=2181 \
-e ZOOKEEPER_CLIENT_PORT2=2182 \
-e ZOOKEEPER_CLIENT_PORT3=2183 \
-e KAFKA_INIT_CONFIG=yes -v /opt/kafka2:/opt/kafkadata kafka


docker run -it --name kafka3 -p 9093:9093 -p 2183:2183 -p 3003-3004:3003-3004 -e ZOOKEEPER_ID=3 \
-e KAFKA_BROKER_ID=2 -e KAFKA_PORT=9093  -e KAFKA_HOST_IP=$(hostname -i) \
-e ZOOKEEPER_SERVER1="192.168.0.178:2999:3000" \
-e ZOOKEEPER_SERVER2="192.168.0.158:3001:3002" \
-e ZOOKEEPER_SERVER3="192.168.0.158:3003:3004" \
-e ZOOKEEPER_CLIENT_PORT1=2181 \
-e ZOOKEEPER_CLIENT_PORT2=2182 \
-e ZOOKEEPER_CLIENT_PORT3=2183 \
-e KAFKA_INIT_CONFIG=yes -v /opt/kafka3:/opt/kafkadata kafka

docker run -it --name kafka4 -p 9094:9094 -e NODE_USAGE="kafka" \
-e KAFKA_BROKER_ID=3 -e KAFKA_PORT=9094 -e KAFKA_HOST_IP=$(hostname -i) \
-e ZOOKEEPER_SERVER1="192.168.0.178:2999:3000" \
-e ZOOKEEPER_SERVER2="192.168.0.158:3001:3002" \
-e ZOOKEEPER_SERVER3="192.168.0.158:3003:3004" \
-e ZOOKEEPER_CLIENT_PORT1=2181 -e ZOOKEEPER_CLIENT_PORT2=2182 -e ZOOKEEPER_CLIENT_PORT3=2183 \
-e KAFKA_INIT_CONFIG=yes -v /opt/kafka4:/opt/kafkadata kafka
</pre>
