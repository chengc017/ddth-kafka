ddth-kafka 
==========

DDTH's Kafka Libraries and Utilities: simplify using [Apache Kafka](http://kafka.apache.org/).

Project home:
[https://github.com/DDTH/ddth-kafka](https://github.com/DDTH/ddth-kafka)

OSGi environment: `ddth-kafka` is packaged as an OSGi bundle.


## License ##

See LICENSE.txt for details. Copyright (c) 2014 Thanh Ba Nguyen.

Third party libraries are distributed under their own licenses.


## Installation #

Latest release version: `1.0.3`. See [RELEASE-NOTES.md](RELEASE-NOTES.md).

Maven dependency:

```xml
<dependency>
	<groupId>com.github.ddth</groupId>
	<artifactId>ddth-kafka</artifactId>
	<version>1.0.3</version>
</dependency>
```


## Usage ##

**Initialize a Kafka client:**

```java
import com.github.ddth.kafka.KafkaClient;
...
String zookeeperConnString = "localhost:2181/kafka";
KafkaClient kafkaClient = new KafkaClient(zookeeperConnString);
kafkaClient.init();
```

**Publish message(s):**

```java
import com.github.ddth.kafka.KafkaMessage;
...
KafkaMessage msg = new KafkaMessage("topic", "message-content-1");
kafkaClient.sendMessage(msg);

msg = new KafkaMessage("topic", "msg-key", "message-content-2");
kafkaClient.sendMessage(msg);

//you can also send multiple messages at once
KafkaMessage[] msgs = ...;
kafkaClient.sendMessages(msgs);
```

**Consume one single message:**

```java
final String consumerGroupId = "my-group-id";
final boolean consumeFromBeginning = true;
final String topic = "topic-name";

//consume one message from a topic, this method blocks until message is available
KafkaMessage msg = consumer.consumeMessage(consumerGroupId, consumeFromBeginning, topic);

//consume one message from a topic, wait up to 3 seconds for message to become available
KafkaMessage msg = consumer.consumeMessage(consumerGroupId, consumeFromBeginning, topic, 3, TimeUnit.SECONDS);
```

**Consume messages using message listener:**

```java
import com.github.ddth.kafka.IKafkaMessageListener;
...
IKafkaMessageListener messageListener = new IKafkaMessageListener() {
    @Override
    public void onMessage(KafkaMessage message) {
        //do something with the received message
    }
}
kafkaClient.addMessageListener(consumerGroupId, consumeFromBeginning, topic, msgListener);

//stop receving message
kafkaClient.removeMessageListener(consumerGroupId, topic, messageListener);
```

**Destroy Kafka client when done:**

```java
kafkaClient.destroy();
```

> #### Consumer Group Id ####
> Each consumer is associated with a consumer-group-id. Kafka remembers offset of the last message that the consumer has consumed. Thus, consumer can resume from where it has left.
>
> The very first time a consumer consumes a message from an existing topic, it can choose to consume messages from the beginning (i.e. consume messages that already exist in the topic), or consume only new messages (e.g. existing messages in the topic are ignored). This is controlled by the `consume-from-beginning` parameter.
> 
> If two or more comsumers have same one group id, and consume messages from a same topic: messages will be consumed just like a queue: no message is consumed by more than one consumer. Which consumer consumes which message is undetermined.
>
> If two or more comsumers with different group ids, and consume messages from a same topic: messages will be consumed just like publish-subscribe pattern: one message is consumed by all consumers.
