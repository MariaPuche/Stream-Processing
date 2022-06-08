# **Definición Topic**

A topic is a basic component of Apache Kafka, it can be seen as a "container" for messages. More specifically, it can be defined as the category or criteria by which messages can be grouped in Kafka. A collection of messages form a topic.

Their characteristics are:

- There can be 1 or more topics, there is no limitation.
- Each topic has a specific configuration and, therefore, an independent structure within Kafka (this information is obtained from the Zookeeper)
- It is used for writing and reading messages: A product introduces the message (1 producer each time) and a consumer extracts the message (can have 0 or N consumers)
- Each topic has a Topic Log (Message disk storage structure, messages are structured and organized) and partition (parts into which the topic Log can be divided, 1 to N partitions, default = 1)

Once the concept of Topic has been introduced, we proceed to create it.

# **CREATE TOPIC**

First, you have to locate in opt/bitnami/kafka/bin:

        cd opt/bitnami/kafka/bin

To create a topirc, you have to indicate:

- Zookeeper server and port.
- Identifying: Name of topic.
- If we want create, delete or alter topic.
- Number of partitions that topic log will split. It depend on the number of brockets.
- Replication factor: Number of copies of the partitions that will be maintained in the cluster. It must be less or equal to the number of brokers.

We are using the version 2.8.1, so we don't conect the topic with zookeeper.

        kafka-topics.sh --create --bootstrap-server 127.0.0.1:9092 --replication-factor 1 --partitions 3 --topic first_topic

In this case, we'll be create a topic with 3 partitions, 1 replication factor, its name is first_topic, the Zookeeper server is 127.0.0.1 and the port is 2181. You remember you have to open the two terminal where you configurate kafka and zookeeper.

To check that we have created the topic, we are goingo to list all the topic we have created. 

        kafka-topics.sh --list --bootstrap-server 127.0.0.1:9092

To describe the characteristic, we use the following comand:

        kafka-topics.sh --describe --bootstrap-server 127.0.0.1:9092 --topic first_topic 

# **DROP TOPIC**

In this seccion, we'll create a topic with 6 partitions and 1 replication factor. Then, we delete this.

        kafka-topics.sh --create --bootstrap-server 127.0.0.1:9092 --topic second_topic --partitions 6 --replication-factor 1

Now, we are going to list all the topic: first_topic and second_topic. 

        kafka-topics.sh --bootstrap-server 127.0.0.1:9092 --list

The next step is delete second_topic.

        kafka-topics.sh --delete  --bootstrap-server 127.0.0.1:9092 --topic second_topic

In the end, we list all the topic again and we can see that there is only one cluster, first_topic.

        kafka-topics.sh --bootstrap-server 127.0.0.1:9092 --list

# **WRITE EVENTS INTO THE TOPIC**

First, we list all available options of the comand to know the argument and mandatory parameters.

        kafka-console-producer.sh

Run the console producer client to write a few events into your topic. By default, each line we enter will result in a separate event being written to the topic.

        kafka-console-producer.sh --broker-list 127.0.0.1:9092 --topic first_topic

We are going to introduce the following events:

![Marcado 1](Events.png)

We can stop the producer client with Ctrl-C at any time.

In addition, we can add properties to the producer.  The Kafka Producer configures acks to control record durability. The ”all” acks setting ensures full commit of record to all replicas and is most durable and least fast setting. The Kafka Producer can automatically retry failed requests.

        kafka-console-producer.sh --broker-list 127.0.0.1:9092 --topic first_topic --producer-property acks=all

We are going to introduce the following events:

![Marcado 2](Events2.png)

If we try to write an event into the topic that not exists, Kafka create this alone.

        kafka-console-producer.sh --broker-list 127.0.0.1:9092 --topic new_topic

        kafka-topics.sh --bootstrap-server 127.0.0.1:9092 --list

We can see there are two topic: first_topic and new_topic, created alone when we introce the events. This have the following properties:

        kafka-topics.sh --bootstrap-server 127.0.0.1:9092 --topic new_topic --describe

# **READ THE EVENTS**
First, we list all available options of the comand to know the argument and mandatory parameters.

        kafka-console-consumer.sh

In this case, it doesn't read the entire topic, only reads the messages that arrive once lauched the consumer. 

        kafka-console-consumer.sh --bootstrap-server 127.0.0.1:9092 --topic first_topic

So, we open other terminal to write the events into the topics. 

        kafka-console-producer.sh --broker-list 127.0.0.1:9092 --topic first_topic

On the other hand, we can read the entire topic, so, you have to lauch the consumer with following properties (first, cancel the last consumer with Ctrl + C but the last producer continue on working):

        kafka-console-consumer.sh --bootstrap-server 127.0.0.1:9092 --topic first_topic --from-beginning

Finally, you cancel the consumer and producer. 


# **CONSUMER GROUP**

A consumer group is a group of consumers that share the same group id. When a topic is consumed by consumers in the same group, every record will be delivered to only one consumer. As the official documentation states: “If all the consumer instances have the same consumer group, then the records will effectively be load-balanced over the consumer instances.”

In the same terminal where we run the consumer:

        kafka-console-consumer.sh --bootstrap-server 127.0.0.1:9092 --topic first_topic --group my-first-application

In other terminal where we run the producer:

        kafka-console-producer.sh --broker-list 127.0.0.1:9092 --topic first_topic

In this terminal, we have to write events and we can see that appears in the other terminal.

Now, we open other terminal and launch other consumer with the same consumer group and write menssages in the producer shell.

        kafka-console-consumer.sh --bootstrap-server 127.0.0.1:9092 --topic first_topic  --group my-first-application

We can see messages distribute in differents consumers. If we want, we could lauch other consumer in other shell and see how the messages distribute.

        kafka-console-consumer.sh --bootstrap-server 127.0.0.1:9092 --topic first_topic  --group my-first-application

Now, let's launch other consumer with a other group, my-second-application, and we read it from the beggining.       
       
        kafka-console-consumer.sh --bootstrap-server 127.0.0.1:9092 --topic first_topic --group my-second-application --from-beginning 

# **EXPLORATE CONSUMER GROUPS**

We want to see the consumer group we have created:

        kafka-consumer-groups.sh --bootstrap-server 127.0.0.1:9092 --list

To visualize the offset by partitions:

        kafka-consumer-groups.sh --bootstrap-server 127.0.0.1:9092 --describe --group my-second-application       

![Marcado 3](second_application.png)


## **EJERCICIOS RESUMEN**

#### **1. Create a topic named "topic_app" with 3 partitions and replication-factor = 1.**

        kafka-topics.sh --create --bootstrap-server 127.0.0.1:9092 --replication-factor 1 --partitions 3 --topic topic_app

#### **2. Create a producer  that insert menssages into the topic topic_app.**

        kafka-console-producer.sh --broker-list 127.0.0.1:9092 --topic topic_app

#### **3. Create 2 consumers that are part of a consumption group called "my_app".**

You have to launch the comands in diferents shells.

        kafka-console-consumer.sh --bootstrap-server 127.0.0.1:9092 --topic topic_app  --group my_app

        kafka-console-consumer.sh --bootstrap-server 127.0.0.1:9092 --topic topic_app  --group my_app


#### **5. Launch comands to list the topics, consumer groups and describe it**

List all topic created.

        kafka-topics.sh --list --bootstrap-server 127.0.0.1:9092

Describe the topic *topic_app*.

        kafka-topics.sh --bootstrap-server 127.0.0.1:9092 --topic topic_app --describe

Read the messages from beginning into the *topic_app*.

        kafka-console-consumer.sh --bootstrap-server 127.0.0.1:9092 --topic topic_app --from-beginning

List all consumer groups created:

        kafka-consumer-groups.sh --bootstrap-server 127.0.0.1:9092 --list

Describe the consumer group *my_app*.

        kafka-consumer-groups.sh --bootstrap-server 127.0.0.1:9092 --describe --group my_app



