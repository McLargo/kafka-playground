# Kafka playground

In this repository I will explore Kafka, from installation, configuration and
integration with Python and Golang.

For this playground, only one kafka broker is used, but it can be scaled
horizontally by adding more brokers to the cluster.

## Concepts

Small introduction to the main concepts of Kafka.

### Broker

A broker is the service that store messages in the different partitions of the
topics, handling the partitions and replication. Also, it handles the requests
for the producers and consumers.

### Topics

Topic is the fundamental concept in Kafka. It is a category to which messages
are send and stored from the producers, waiting to be consumed by the consumers.

Topics can have partitions, which is the number of segments that the topic will
be divided the data into, allowing to consume messages in parallel. Messages in
a partitions are sorted, but messages across partitions are not ordered. With N
partitions, you can have N consumers consuming messages in parallel. Even you
can have producers writing to specific partitions (you can make sure by sending
same key).

The replication factor is the number of copies of the data that will be
stored in the cluster. For each partition, exists a leader and N-1 followers.
The leader is the one that receives the messages and replicates them to the
followers. If at some point the leader fails, one of the followers will be
elected as the new leader.

### Producer

Producer is the service that sends messages to a topic. It can send messages to
a specific partition, or let Kafka decide which partition to use. A message can
have headers, a key and a value. The key is used to determine which partition
the message will be sent to (messages with same key will go to same partition).

### Consumer

Consumer is the service that reads messages from a topic. It can read messages
from a specific partition, or from all partitions. It can also read messages
from a specific offset, or from the beginning of the topic.

Consumers groups are when multiple consumers work together to consume messages
from a topic. Kafka assigned to consumer inside same consumer group a subset of
the partitions of the topic, allowing to consume messages in parallel. As we see
before, messages are not replicated in different partitions, there is no risk of
consuming the same message twice. If a consumer fails, Kafka will reassign
partitions automatically to the other consumers in the group.

## Installation

To install the dependencies locally, run:

```bash
task build
```

Using docker compose, Kafka and its dependencies are build and started in
detached mode.

## Visualization

To visualize Kafka topics and messages, I use Redpanda, a lightweight Kafka UI.

Just after `task build`, you can access the UI at `http://localhost:8080`.

This interface not only allows you to see the topics and messages, but also to
create new topics, delete them, and produce messages to them.

## Kafka commands

Kafka commands are available in the `kafka` container. You can access it by
running:

```bash
docker exec -it kafka /bin/bash
```

From inside the container, you can use the this commands to interact with Kafka.
You can create/delete/list topics, and produce or consume messages...

### Managing topics

with command `kafka-topics` you can create, delete and list topics.

To facilitate the use of this command, use task commands:

```bash
task kafka-topics -> list topics
task kafka-topics-create TOPIC=`name` -> create a topic
task kafka-topics-delete TOPIC=`name` -> delete a topic
task kafka-topics-describe TOPIC=`name` -> describe a topic
```

When creating a topic, you can specify the number of partitions and the
replication factor. For example:

```bash
task kafka-topics-create TOPIC=`name` PARTITIONS=3 REPLICATION_FACTOR=1
```

There are other operations available on topics, like alter a topic
configuration.

### Produce/consume messages

Basic commands to produce and consume messages are available by
`kafka-console-producer` and `kafka-console-consumer`.

To produce manually messages to a topic, you can use the following command:

```bash
task kafka-produce-messages TOPIC=`name`
```

To consume messages from a topic, you can use the following command:

```bash
task kafka-consume-messages TOPIC=`name`
```

You can enrich the commands with additional parameters, such as

- `--from-beginning`: to consume messages from the beginning of the topic
- `--offset`: to consume messages from a specific offset
- `--partition`: to consume messages from a specific partition
- `--property print.key=true`: to print the key of the messages
- `--group`: to consume messages as a consumer group

## References

- [Kafka guide](https://medium.com/@t.m.h.v.eijk/list/kafka-the-definitive-guide-ad3ff24f54ea)
