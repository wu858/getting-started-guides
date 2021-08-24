---
seo:
  title: Getting Started with Apache Kafka and Spring Boot
  description: Step-by-step guide to building a Spring Boot client application for Kafka 
hero:
  title: Getting Started with Apache Kafka and Spring Boot
  description: Step-by-step guide to building a Spring Boot client application for Kafka 
---

# Getting Started with Apache Kafka and Spring Boot

## Introduction

In this tutorial, you will run a Spring Boot client application that produces
messages to and consumes messages from an Apache Kafka® cluster. The
tutorial will walk you through setting up a Kafka cluster if you
do not already have access to one.

<div class="alert-primary">
Note: This tutorial focuses on a simple application to get you started.
If you want to build more complex applications and microservices for data in motion—with powerful features such as real-time joins, aggregations, filters, exactly-once processing, and more—check out the <a href="/learn-kafka/kafka-streams/get-started/">Kafka Streams 101 course</a>, which covers the
<a href="https://docs.confluent.io/platform/current/streams/index.html">Kafka Streams client library</a>.
</div>


## Prerequisites

This guide assumes that you already have:

[Maven](https://maven.apache.org) installed

[Java 11](https://www.oracle.com/java/technologies/javase-downloads.html)
installed and configured as the current java version for the environment

Later in this tutorial you will set up a new Kafka cluster or connect
to an existing one. If you wish to run a local Kafka cluster, you will
also need [Docker](https://docs.docker.com/get-docker/) installed
(this tutorial uses the new `docker compose` command, see the [Docker
documentation for more information](https://docs.docker.com/compose/cli-command/#new-docker-compose-command)).

## Create Project

Create a new directory anywhere you'd like for this project:

```sh
mkdir kafka-springboot-getting-started && cd kafka-springboot-getting-started
```

Create the following Gradle build file for the project, named
`pom.xml`:

```xml file=pom.xml
```

## Kafka Setup

We are going to need a Kafka Cluster for our client application to
operate with. This dialog can help you configure your Confluent Cloud
cluster, create a Kafka cluster for you, or help you input an existing
cluster bootstrap server to connect to.

<p>
  <div class="select-wrapper">
    <select data-context="true" name="kafka.broker">
      <option value="">Select Kafka Broker</option>
      <option value="cloud">Confluent Cloud</option>
      <option value="local">Local</option>
      <option value="other">Other</option>
    </select>
  </div>
</p>

<section data-context-key="kafka.broker" data-context-value="cloud">

<p>
  <label for="kafka-broker-server">Bootstrap Server</label>
  <input id="kafka-broker-server" data-context="true" name="kafka.broker.server" placeholder="cluster-id.region.provider.confluent.cloiud:9092" />
</p>

Paste your Confluent Cloud bootstrap server setting above and the
tutorial will fill in the appropriate configuration for
you.

You can obtain your Confluent Cloud Kafka cluster bootstrap server
configuration using the [Confluent Cloud UI](https://confluent.cloud/).

![](../media/cc-cluster-settings.png)

</section>

<section data-context-key="kafka.broker" data-context-value="local">
  
Paste the following file into a `docker-compose.yml` file:

```yaml file=../docker-compose.yml
```

Now start the Kafka broker with: `docker compose up -d`

</section>

<section data-context-key="kafka.broker" data-context-value="other">
  
<p>
  <label for="kafka-broker-server">Bootstrap Server</label>
  <input id="kafka-broker-server" data-context="true" name="kafka.broker.server" placeholder="broker:9092" />
</p>

Paste your Kafka cluster bootstrap server URL above and the tutorial will
fill it into the appropriate configuration for you.

</section>

## Configuration

Create a directory for the application resource file:

```sh
mkdir -p src/main/resources
```

Paste the following configuration data into a file located at `src/main/resources/application.yaml`

<section data-context-key="kafka.broker" data-context-value="cloud">

The below configuration includes the required settings for a connection
to Confluent Cloud including the bootstrap servers configuration you
provided. 

![](../media/cc-create-key.png)

When using Confluent Cloud you will be required to provide an API key
and secret authorizing your application to produce and consume. You can
use the [Cloud UI](https://confluent.cloud/) to create a key for
you.

Take note of the API key and secret and add them to the configuraiton file.
The `sasl.username` value should contain the API key, 
and the `sasl.password` value should contain the API secret.

```ini file=getting-started-cloud.properties
```

</section>

<section data-context-key="kafka.broker" data-context-value="local">

```ini file=getting-started-local.properties
```

</section>

<section data-context-key="kafka.broker" data-context-value="other">

The below configuration file includes the bootstrap servers
configuration you provided. If your Kafka Cluster requires different
client security configuration, you may require [different
settings](https://kafka.apache.org/documentation/#security).

```ini file=getting-started-other.properties
```

</section>

## Create Topic

Events in Kafka are organized and durably stored in named topics. Topics
have parameters that determine the performance and durability guarantees
of the events that flow through them.

Create a new topic, `purchases`, which we will use to produce and consume
events.

<section data-context-key="kafka.broker" data-context-value="cloud">

![](../media/cc-create-topic.png)

When using Confluent Cloud, you can use the [Cloud
UI](https://confluent.cloud/) to create a topic. Create a topic
with 1 partition and defaults for the remaining settings.

</section>

<section data-context-key="kafka.broker" data-context-value="local">

We'll use the `kafka-topics` command located inside the local running
Kafka broker:

```sh file=../create-topic.sh
```
</section>

<section data-context-key="kafka.broker" data-context-value="other">

Depending on your available Kafka cluster, you have multiple options
for creating a topic. You may have access to [Confluent Control
Center](https://docs.confluent.io/platform/current/control-center/index.html),
where you can [create a topic with a
UI](https://docs.confluent.io/platform/current/control-center/topics/create.html). You
may have already installed a Kafka distribution, in which case you can
use the [kafka-topics command](https://kafka.apache.org/documentation/#basic_ops_add_topic).
Note that, if your cluster is centrally managed, you may need to
request the creation of a topic from your operations team.

</section>

## Build Producer

Create a directory for the Java files in this project:

```sh
mkdir -p src/main/java/examples
```

We will use the `SpringBootApplication` annotation for ease of use, since it provides auto-configuration and component scan.
Paste the following Java code into a file located at `src/main/java/examples/SpringBootWithKafkaApplication.java`

```java file=src/main/java/examples/SpringBootWithKafkaApplication.java
```

Create a service to listen for produce commands.
Paste the following Java code into a file located at `src/main/java/examples/controllers/KafkaController.java `

```java file=src/main/java/examples/controllers/KafkaController.java
```

Finally, the Kafka Producer.
Paste the following Java code into a file located at `src/main/java/examples/Producer.java`

```java file=src/main/java/examples/Producer.java
```

You can test the code before preceding by compiling with:

```sh
mvn clean compile
```
And you should see:

```
BUILD SUCCESS
```

## Build Consumer

Paste the following Java code into a file located at `src/main/java/examples/Consumer.java`

```java file=src/main/java/examples/Consumer.java
```

Once again, you can compile the code before preceding by with:

```sh
mvn compile
```

And you should see:

```
BUILD SUCCESSFUL
```

## Produce Events

To build a JAR that we can run from the command line, first run:

```sh
mvn package
```

And you should see:

```
BUILD SUCCESSFUL
```

Run the following command to run the Spring Boot application and all
its components.

```sh
mvn spring-boot:run
```

In a separate terminal window, produce some data events to the `purchases` topic.

```sh
curl -X POST -F "key=awalther" -F "value=t-shirts" http://localhost:9000/produce
curl -X POST -F "key=htanaka" -F "value=t-shirts" http://localhost:9000/produce
curl -X POST -F "key=htanaka" -F "value=batteries" http://localhost:9000/produce
curl -X POST -F "key=eabara" -F "value=t-shirts" http://localhost:9000/produce
curl -X POST -F "key=htanaka" -F "value=t-shirts" http://localhost:9000/produce
curl -X POST -F "key=jsmith" -F "value=book" http://localhost:9000/produce
curl -X POST -F "key=awalther" -F "value=t-shirts" http://localhost:9000/produce
curl -X POST -F "key=jsmith" -F "value=batteries" http://localhost:9000/produce
curl -X POST -F "key=jsmith" -F "value=gift card" http://localhost:9000/produce
curl -X POST -F "key=eabara" -F "value=t-shirts" http://localhost:9000/produce
```

In the Spring Boot application window, you should see output that includes:

```
2021-08-24 18:26:09.530  INFO 21133 --- [ad | producer-1] examples.Producer                        : Produced event to topic purchases: key = awalther   value = t-shirts

2021-08-24 18:26:09.580  INFO 21133 --- [ad | producer-1] examples.Producer                        : Produced event to topic purchases: key = htanaka    value = t-shirts

2021-08-24 18:26:09.623  INFO 21133 --- [ad | producer-1] examples.Producer                        : Produced event to topic purchases: key = htanaka    value = batteries

2021-08-24 18:26:09.665  INFO 21133 --- [ad | producer-1] examples.Producer                        : Produced event to topic purchases: key = eabara     value = t-shirts

2021-08-24 18:26:09.711  INFO 21133 --- [ad | producer-1] examples.Producer                        : Produced event to topic purchases: key = htanaka    value = t-shirts

2021-08-24 18:26:09.754  INFO 21133 --- [ad | producer-1] examples.Producer                        : Produced event to topic purchases: key = jsmith     value = book

2021-08-24 18:26:09.799  INFO 21133 --- [ad | producer-1] examples.Producer                        : Produced event to topic purchases: key = awalther   value = t-shirts

2021-08-24 18:26:09.843  INFO 21133 --- [ad | producer-1] examples.Producer                        : Produced event to topic purchases: key = jsmith     value = batteries

2021-08-24 18:26:09.887  INFO 21133 --- [ad | producer-1] examples.Producer                        : Produced event to topic purchases: key = jsmith     value = gift card

2021-08-24 18:26:09.930  INFO 21133 --- [ad | producer-1] examples.Producer                        : Produced event to topic purchases: key = eabara     value = t-shirts
```

## Consume Events

Since the consumer was started automatically with the application,
the output from the Spring Boot application will include the consume events interspersed with the produce events.
In the same output as the previous section, you should see the consumed events.

```
2021-08-24 18:26:09.535  INFO 21133 --- [ntainer#0-0-C-1] examples.Producer                        : Consumed event from topic purchases: key = awalther   value = t-shirts

2021-08-24 18:26:09.581  INFO 21133 --- [ntainer#0-0-C-1] examples.Producer                        : Consumed event from topic purchases: key = htanaka    value = t-shirts

2021-08-24 18:26:09.624  INFO 21133 --- [ntainer#0-0-C-1] examples.Producer                        : Consumed event from topic purchases: key = htanaka    value = batteries

2021-08-24 18:26:09.666  INFO 21133 --- [ntainer#0-0-C-1] examples.Producer                        : Consumed event from topic purchases: key = eabara     value = t-shirts

2021-08-24 18:26:09.711  INFO 21133 --- [ntainer#0-0-C-1] examples.Producer                        : Consumed event from topic purchases: key = htanaka    value = t-shirts

2021-08-24 18:26:09.755  INFO 21133 --- [ntainer#0-0-C-1] examples.Producer                        : Consumed event from topic purchases: key = jsmith     value = book

2021-08-24 18:26:09.800  INFO 21133 --- [ntainer#0-0-C-1] examples.Producer                        : Consumed event from topic purchases: key = awalther   value = t-shirts

2021-08-24 18:26:09.844  INFO 21133 --- [ntainer#0-0-C-1] examples.Producer                        : Consumed event from topic purchases: key = jsmith     value = batteries

2021-08-24 18:26:09.887  INFO 21133 --- [ntainer#0-0-C-1] examples.Producer                        : Consumed event from topic purchases: key = jsmith     value = gift card

2021-08-24 18:26:09.931  INFO 21133 --- [ntainer#0-0-C-1] examples.Producer                        : Consumed event from topic purchases: key = eabara     value = t-shirts
```

## Where next?

- For a Spring Boot example using Schema Registry and Avro, check out the
  [Spring Boot example](https://docs.confluent.io/platform/current/tutorials/examples/clients/docs/java-springboot.html).
- Complete the free, online [Introduction to Spring Boot for Confluent Cloud](https://developer.confluent.io/learn-kafka/spring/confluent-cloud/) course.
- If you want to build more complex applications and microservices—with powerful features such as real-time joins, aggregations, filters, exactly-once processing, and more—check out the [Kafka Streams 101 course](/learn-kafka/kafka-streams/get-started/).
- For information on testing in the Kafka ecosystem, check out
  [Testing Event Streaming Apps](/learn/testing-kafka).
- Interested in performance tuning of your event streaming applications?
  Check out the [Kafka Performance resources](/learn/kafka-performance/).