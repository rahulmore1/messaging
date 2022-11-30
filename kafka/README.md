# Introduction to Kafka

Official [Docs](https://kafka.apache.org/)


# Topic .. Test

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
