# Deploy Kafka 3.0
Deploy kafka 3.0 with or without Zookeeper. the container is able to run into 3 modes, both=kafka+zookeeper, kafka only or zookeeper only

## Assumptions
Host server: 192.168.0.158
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
3 zookepper servers + 2 kafka only servers, 2 of them only run zookeeper and another one running kafka+zookeeper, one running kafka only

<pre>
docker run -it --name zookeeper1 -p 2181:2181 -p 2999-3000:2999-3000 -e NODE_USAGE="zookeeper" \
-e ZOOKEEPER_SERVER1="192.168.0.158:2999:3000" \
-e ZOOKEEPER_SERVER2="192.168.0.158:3001:3002" \
-e ZOOKEEPER_SERVER3="192.168.0.158:3003:3004" \
-e ZOOKEEPER_CLIENT_PORT1=2181 -e ZOOKEEPER_CLIENT_PORT2=2182 -e ZOOKEEPER_CLIENT_PORT3=2183 \
-e KAFKA_INIT_CONFIG=yes -v /opt/zookeeper1:/opt/kafkadata kafka

docker run -it --name zookeeper2 -p 2182:2182 -p 3001-3002:3001-3002 -e NODE_USAGE="zookeeper" -e ZOOKEEPER_ID=2 \
-e ZOOKEEPER_SERVER1="192.168.0.158:2999:3000" \
-e ZOOKEEPER_SERVER2="192.168.0.158:3001:3002" \
-e ZOOKEEPER_SERVER3="192.168.0.158:3003:3004" \
-e ZOOKEEPER_CLIENT_PORT1=2181 -e ZOOKEEPER_CLIENT_PORT2=2182 -e ZOOKEEPER_CLIENT_PORT3=2183 \
-e KAFKA_INIT_CONFIG=yes -v /opt/zookeeper2:/opt/kafkadata kafka

docker run -it --name kafka1 -p 9091:9091 -p 2183:2183 -p 3003-3004:3003-3004 -e ZOOKEEPER_ID=3 \
-e ZOOKEEPER_SERVER1="192.168.0.158:2999:3000" \
-e ZOOKEEPER_SERVER2="192.168.0.158:3001:3002" \
-e ZOOKEEPER_SERVER3="192.168.0.158:3003:3004" \
-e ZOOKEEPER_CLIENT_PORT1=2181 -e ZOOKEEPER_CLIENT_PORT2=2182 -e ZOOKEEPER_CLIENT_PORT3=2183 \
-e KAFKA_INIT_CONFIG=yes -v /opt/kafka1:/opt/kafkadata kafka

docker run -it --name kafka2 -p 9092:9092 -e NODE_USAGE="kafka" -e KAFKA_BROKER_ID=2 -e KAFKA_PORT=9092 \
-e ZOOKEEPER_SERVER1="192.168.0.158:2999:3000" \
-e ZOOKEEPER_SERVER2="192.168.0.158:3001:3002" \
-e ZOOKEEPER_SERVER3="192.168.0.158:3003:3004" \
-e ZOOKEEPER_CLIENT_PORT1=2181 -e ZOOKEEPER_CLIENT_PORT2=2182 -e ZOOKEEPER_CLIENT_PORT3=2183 \
-e KAFKA_INIT_CONFIG=yes -v /opt/kafka2:/opt/kafkadata kafka
</pre>
