# Introduction to Kafka

Official [Docs](https://kafka.apache.org/)

## Building a Docker file

As always, we start with a `dockerfile` </br>
We can build our `dockerfile`

```
cd .\messaging\kafka\
docker build . -t aimvector/kafka:2.7.0

```

## Exploring the Kafka Install

We can then run it to explore the contents:

```
docker run --rm --name kafka -it aimvector/kafka:2.7.0 bash

ls -l /kafka/bin/
cat /kafka/config/server.properties
```

We can use the `docker cp` command to copy the file out of our container:

```
docker cp kafka:/kafka/config/server.properties ./server.properties
docker cp kafka:/kafka/config/zookeeper.properties ./zookeeper.properties
```

Note: We'll need the Kafka configuration to tune our server and Kafka also requires
at least one Zookeeper instance in order to function. To achieve high availability, we'll run
multiple kafka as well as multiple zookeeper instances in the future

# Zookeeper

Let's build a Zookeeper image. The Apache folks have made it easy to start a Zookeeper instance the same way as the Kafka instance by simply running the `start-zookeeper.sh` script.

```
cd ./zookeeper
docker build . -t aimvector/zookeeper:2.7.0
cd ..
```

Let's create a kafka network and run 1 zookeeper instance

```
docker network create kafka
docker run -d `
--rm `
--name zookeeper-1 `
--net kafka `
-v ${PWD}/config/zookeeper-1/zookeeper.properties:/kafka/config/zookeeper.properties `
aimvector/zookeeper:2.7.0

docker logs zookeeper-1
```

# Kafka - 1

```
docker run -d `
--rm `
--name kafka-1 `
--net kafka `
-v ${PWD}/config/kafka-1/server.properties:/kafka/config/server.properties `
aimvector/kafka:2.7.0

docker logs kafka-1
```

# Kafka - 2

```
docker run -d `
--rm `
--name kafka-2 `
--net kafka `
-v ${PWD}/config/kafka-2/server.properties:/kafka/config/server.properties `
aimvector/kafka:2.7.0
```

# Kafka - 3

```
docker run -d `
--rm `
--name kafka-3 `
--net kafka `
-v ${PWD}/config/kafka-3/server.properties:/kafka/config/server.properties `
aimvector/kafka:2.7.0
```


# Topic

Let's create a Topic that allows us to store `Order` information. </br>
To create a topic, Kafka and Zookeeper have scripts with the installer that allows us to do so. </br>

Access the container:
```
docker exec -it zookeeper-1 bash
```
Create the Topic:
```
/kafka/bin/kafka-topics.sh \
--create \
--zookeeper zookeeper-1:2181 \
--replication-factor 1 \
--partitions 3 \
--topic Orders
```

Describe our Topic:
```
/kafka/bin/kafka-topics.sh \
--describe \
--topic Orders \
--zookeeper zookeeper-1:2181
```

## Start the containers

```
cd messaging\kafka

docker compose build
docker compose up -d
```
