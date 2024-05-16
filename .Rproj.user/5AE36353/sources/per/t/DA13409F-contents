# Getting Started with Apache Kafka on Jakarta EE and Payara Server

# *Contents*

Introduction ………………………………………………………………………………….1

Messaging…………………………………………………………………………………….1

What is Apache Kafka?……………………………………………………………………2

Why Choose Apache Kafka?…………………………………………………………….2

Connecting To Kafka From Jakarta EE………………………………………………..2

Requirements………………………………………………………………………………..2

Connecting From Jakarta EE…………………………………………………………….4

Summary………………………………………………………………………………………8

Ready for Production?…………………………………………………………………….9

Consider Payara Enterprise……………………………………………………………..9

# *Introduction*

In the world of software development, applications often need to communicate with each other. Traditionally, this was done through synchronous methods like REST APIs, whereby one application makes a request and waits for an immediate response. While REST APIs are widely used and rela- tively straightforward to implement, they can present a number of challenges in complex distributed systems:

• **Tight Coupling:** RESTful applications often have direct dependencies on each other. If one service fails or is slow, it can directly impact the responsiveness of other services within the chain of calls.

• **Scalability:** It can be difficult to scale REST APIs horizontally under extremely high loads. Handling a huge number of concurrent requests might require complex load balancing and caching strategies.

• **Availability:** In a REST architecture, if the service providing an API endpoint is unavailable, the applications consuming that API may be disrupted too.

• **Limited:** Communication Patterns: REST primarily focuses on request-response interac- tions. More intricate scenarios involving event-driven communication or broadcasting mes- sages to multiple subscribers can be harder to model.

# *Messaging*

Message driven architectures came about as remedies to some of the REST challenges. At its core, messaging facilitates the exchange of information between different parts of a system, or even com- pletely different systems, enabling users to implement decoupled, asynchronous communications. This decoupling makes it possible for different parts of a system to interact without being directly connected, enhancing scalability and fault tolerance. Applications can therefore communicate by sending messages to queues or topics. Key advantages include:

• **Loose Coupling:** Applications don’t need to directly know about each other or be online at the same time.

• **Reliability:** Messaging systems often guarantee delivery, ensuring messages aren’t lost even if parts of the system fail.

• **Scalability:** Messaging lets you easily add more producers or consumers of messages to handle increased loads. There are many messaging platforms that, when used as your messaging provider, allow you to design highly scalable applications. One of the most popular of such platforms is Apache Kafka.

# *What is Apache Kafka?*

Apache Kafka is an open-source, distributed message streaming platform designed to handle large volumes of data flowing in real-time. Think of it as a high-performance message highway for your applications. At its core, Kafka combines the following key concepts:

• **Topics:** Data in Kafka are organized into streams, called ‘topics’. Topics are divided into par- titions for scalability and fault tolerance.

• **Producers and Consumers:** Applications can either produce (send) messages to Kafka top- ics or consume (read and process) messages from topics.

• **Persistent Storage:** Kafka durably stores messages on disk, supporting data retention and the ability for consumers to process at their own pace.

• **Distributed Design:** Kafka runs as a cluster of nodes (servers) that work together. This dis- tribution ensures high availability and enables massive scalability.

# *Why Choose Apache Kafka?*

• **Publish-Subscribe Messaging:** Kafka’s pub-sub system fosters loose coupling between applications. Multiple producers can feed into topics and multiple consumers can inde- pendently read from them.

• **High Throughput:** Kafka is optimized for fast ingestion and processing of large volumes of data.

• **Fault Tolerance:** Kafka replicates data across nodes. Even if individual nodes go down, your data and the system remain available.

• **Performance:** Kafka’s streamlined architecture and efficient design deliver excellent perfor- mance, making the solution ideal for big data and real-time applications.

• **Ecosystem:** Kafka’s wide adoption has created a rich ecosystem of tools, connectors and libraries for its seamless integration into various systems.

# *Connecting To Kafka From Jakarta EE*

As a framework-agnostic messaging platform, Kafka can be used from many different development platforms and languages. On Jakarta EE Platforms that use Payara Server, you can connect your application to Kafka through JCA connectors. The biggest advantage of choosing this option is that you can use the familiar Jakarta EE APIs without the need to pull in different libraries.

# *Requirements*

To connect to a Kafka instance from your Jakarta EE application running on Payara Server, you will need the following:

• Docker

• Payara Server Community

• Payara Kafka Cloud Connector

# *Docker*

We will pull in Apache Kafka through docker. This allows us to get up to speed without needing to install anything on the host machine. To pull in the latest version of Kafka, run the following command.

`{bash} docker run -p 9092:9092 -d bashj79/kafka-kraft`

This should pull in a Kafka image and create a container from it. From code, you will connect to this instance through the localhost:9092 address. Keep this in mind, we’ll need it later.

# *Payara Server Community*

Payara Server is a Jakarta EE runtime suitable for running all kinds of Jakarta EE workloads. It implements the Jakarta EE Platform Profile and thus, has the gamut of platform APIs available for your application. This guide assumes you have downloaded a copy of Payara Server Community. If not, please [download](https://www.payara.fish/downloads/payara-platform-community-edition/) one before you continue to follow this guide.

# *Payara Kafka Cloud Connector*

Payara Cloud Connector is a collection of cloud connectors that allow your Jakarta EE application to connect to cloud services like Amazon SQS and Kafka. The aim of cloud connectors is to free you from depending on the specific connection constructs of the cloud service providers. You get to connect to them using familiar Jakarta EE constructs that should work regardless of the underlying service being connected to. To use the Payara Cloud Connector for Kafka, clone/download the pro- ject from GitHub.

`{bash} git clone git@github.com:payara/Cloud-Connectors.git`

Then change directory to the folder and build the project as follows.

`{bash} cd Cloud-Connectors/ mvn clean install`

This should build all the connectors and create .rar files for each. For the Kafka connector, you should have a kafka-rar-1.0.0.rar in the Cloud-Connectors/Kafka/KafkaRAR/target folder. We need to deploy this file to the server and make it globally available to all applications deployed to the servert.

Navigate to your Payara Server Community download folder and change directory to the payara6/ bin folder. Now deploy the kafka-rar.1.0.0.rar with the following command.

`{bash} ./asadmin deploy --target=server /PATH_TO_YOUR_REPOS/Cloud-Connectors/ Kafka/KafkaRAR/target/kafka-rar-1.0.0.rar`

Then change the class loading policy to global as follows

`{bash} ./asadmin set configs.config.server-config.connector-service.class-loading- policy=global`

Now you should have the Kafka connector deployed to your Payara Server Community installation and available to all apps deployed to the server. With the Kafka container running and your server fully set up, let’s connect from the code.

Connecting From Jakarta EE Add the following kafka-jca dependency to your application pom.xml file.

``` xml
<dependency>
            <groupId>fish.payara.cloud.connectors.kafka</groupId>
            <artifactId>kafka-jca-api</artifactId>
            <version>1.0.0</version>
            <type>jar</type>
            <scope>provided</scope>
         </dependency>
```

# *Receiving Kafka Messages*

With the dependency in place, you can now create a Kafka consumer and producer. A consumer consumes message records. Effectively, your consumer subscribes to message topics and gets called when new messages arrive within the topic. The duration of the call is configurable as a polling value. The following shows a full example of a Kafka listener that subscribes to the my-first-topic topic.

``` java
@MessageDriven(activationConfig = {
@ActivationConfigProperty(propertyName = "clientId", propertyValue =
"PayaraMicroMessenger"),
@ActivationConfigProperty(propertyName = "groupIdConfig", propertyValue
= "test-consumer-group"),
@ActivationConfigProperty(propertyName = "topics", propertyValue = "my-
first-topic"),
@ActivationConfigProperty(propertyName = "bootstrapServersConfig",
propertyValue = "localhost:9092"),
@ActivationConfigProperty(propertyName = "autoCommitInterval",
propertyValue = "100"),
@ActivationConfigProperty(propertyName = "retryBackoff", propertyValue
= "1000"),
@ActivationConfigProperty(propertyName = "keyDeserializer",
propertyValue = "org.apache.kafka.common.serialization.
StringDeserializer"),
@ActivationConfigProperty(propertyName = "valueDeserializer",
propertyValue = "org.apache.kafka.common.serialization.
StringDeserializer"),
@ActivationConfigProperty(propertyName = "pollInterval", propertyValue
= "1000"),
})
public class KafkaReceiver implements KafkaListener {
@OnRecord(topics = "my-first-topic")
public void receiveMessage(ConsumerRecord<String, String> messageRecord) {
System.out.println(messageRecord.partition());
     System.out.println(messageRecord.key());
System.out.println(messageRecord.value());
}
```

This is an MDB (Message Driven Bean) that implements the KafkaListener interface. It describes how to listen to a message broker and react to messages sent from it. There are a few things to note about this listener:

• **groupIdConfig →** This is the consumer group this listener is subscribing to.

• **topics →** This is the topic this listener is interested in listening to and be called with new messages sent to the topic.

• **bootstrapServersConfig →** This is the IP address of the Kafka instance. For this guide, this is localhost:9092.

• **pollInterval →** This sets how often the listener will poll the server for messages, in mil- liseconds. This example polls every second. The more often you poll, the more stress you’ll put on your network, but the less often it is, the less responsive your application will be. There is a compromise you’ll have to find for yourself here.

• @OnRecord**(topics={“my-first-topic”}) →** This lists the topics that the listener will observe.

With this in place, our listener will poll the Kafka instance for new messages sent to the my-first-topic topics. The receiveMessage method will be automatically called and passed the ConsumerRecord instance type. The type of message being passed will depend on what your listener expects and what serializers and deserializers have been configured. We leave out those topics for you to explore on your own. Let’s now take a look at creating a message sender.

# *Sending Kafka Messages*

Now that you are able to receive messages from Kafka, you’d probably want to send them too! The JCA API we have as a Maven dependency also has a Kafka specific connection factory. You declare the connection pool and inject it as an instance of this class to use it. The following KafkaSender class shows an example.

``` java
@ConnectionFactoryDefinition(
  name = "java:app/kafka/factory",
  interfaceName = "fish.payara.cloud.connectors.kafka.
  KafkaConnectionFactory",
  resourceAdapter = "kafka-rar-1.0.0",
  minPoolSize = 2,
  maxPoolSize = 20,
  transactionSupport = TransactionSupport.TransactionSupportLevel.
  NoTransaction,
  properties = {"bootstrapServersConfig=localhost:9092",
  "clientId=PayaraMicroMessenger"
   }
)
```

``` java
@Stateless
public class KafkaSender {
       @Resource(lookup = "java:app/kafka/factory")
       private KafkaConnectionFactory factory;
       @Schedule(hour = "*", minute = "*", second = "*/5", persistent = false)
public void sendMessage() {
       try (KafkaConnection conn = factory.createConnection()) {
           conn.send(new ProducerRecord<>("my-first-topic", "jee-
           kafka","Sent from Payara Server Community."));
} catch (Exception ex) {
Logger.getLogger(getClass().getName()).log(Level.SEVERE, null,
ex);
    }
  }
}
```

The top-most annotation @ConnectionFactoryDefinition declares a connection factory program- matically. This can also be configured from the admin console on Payara Server, but by defining it programmatically you ensure that this connection factory is only available to the application it’s defined in. This means you won’t be able to access it from other applications. This block defines all the required values for the connection factory to work.

• **name →** The JNDI name for the connection factory for injection (you can see it being injected in the class).

• **interfaceName →** The interface that is being implemented.

• **resourceAdapter →** The name of the Resource Adapter Archive (RAR) that the interface is in.

• **minPoolSize/maxPoolSize →** The minimum/maximum size of the connection pool to create for this factory.

• **transactionSupport →** Accepts any value in the enum TransactionSupport. TransactionSupportLevel. This defines the level of transactionality that the connection fac- tory has.

• **properties →** This defines extra properties for the connection factory. Possible values are described in the Github readme here: <https://github.com/payara/Cloud-Connectors/tree/> master/Kafka. For this class we need to override the bootstrapServersConfig property to define the Kafka host.

The connection factory created above is injected using the JNDI name specified earlier. It is injected using the interface defined in the API. This is now usable as a connection factory.

The class is annotated with stateless, which is just because the class needs to be an EJB to use an EJB timer. The EJB timer has a scheduled event every 5 seconds called sendMessage(). This will use the created connection factory to send a new record to the topic “my-first-topic”, with the key “jee-kafka” and message value “Sent from Payara Server Community.” Running the app shows the messages printed to the server logs. Remember the listener discussed above is listening to the messages being sent from our KafkaSender.

``` texinfo
[2024-03-22T17:05:40.004+0000] [Payara 6.2024.3] [INFO] [] [] [tid: _
ThreadID=193 _ThreadName=orb-thread-pool-1 (pool #1): worker-3] [timeMillis:
1711127140004] [levelValue: 800] [[
jee-kafka]]
[2024-03-22T17:05:40.004+0000] [Payara 6.2024.3] [INFO] [] [] [tid:
_ThreadID=193 _ThreadName=orb-thread-pool-1 (pool #1): worker-3]
[timeMillis: 1711127140004] [levelValue: 800] [[
Sent from Payara Server Community.]]
```

# *Summary*

This guide introduced you to the world of messaging and the Apache Kafka platform. We covered:

• Messaging Fundamentals: Why messaging systems are valuable for asynchronous commu- nications between applications.

• Apache Kafka: A high-performance, distributed platform for real-time data streams and event-driven systems.

• Payara Setup: How to configure Payara Server Community with the Payara Kafka Cloud Connector to integrate with Kafka.

• Kafka Producer/Consumer: Building basic components within your application to send and receive messages from a specific Kafka topic. Kafka offers a rich set of features for reliable message handling, stream processing, and integrating with various data systems. Resources to Continue Learning:

• Apache Kafka Documentation: The official source for in-depth information.

• Payara Kafka Cloud Connector Docs: Details specifically for Payara users.

# *Ready for Production? Consider Payara Enterprise*

As your application’s requirements evolve and you prepare for production, Payara Enterprise provides you with a fully supported, production-optimized runtime for your Jakarta EE applications. Enjoy stability, performance enhancements and expert support to ensure your mission-critical applications thrive. Learn more about Payara Enterprise and you Power Up your Jakarta EE workloads!

[![](imges/contact.png)](https://www.payara.fish/)

Payara Services Ltd 2024 All Rights Reserved. Registered in England and Wales; Registration Number 09998946 Registered Office: Malvern Hills Science Park, Geraldine Road, Malvern, United Kingdom, WR14 3SZ
