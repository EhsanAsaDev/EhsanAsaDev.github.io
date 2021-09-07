---
layout: post
title: How to use Spring boot and Kafka to build a project based on microservices architecture
comments: false
categories: [ Spring boot ]
tags: [Microservices, Docker , Kafka]
image: assets/images/spring-docker-kafka.png
excerpt: This is a good example of how Spring boot works with Kafka as a message broker.
         In this project, I also use MongoDB and Docker. I show you how to write a test for producers and consumers
         and how to use docker-compose for configuring Kafka in your dev environment ...


---
With Apache Kafka, you shouldn't worry about handling millions of messages quickly and Spring boot provides some useful library for working with Kafka and this is why this stack is very popular these days. I provided a production-ready project
base on microservice architecture on
[GitHub](https://github.com/EhsanAsaDev/car-Location-microservice/)
and in this post, I want to show you how we can use some features in spring boot to using Kafka as a message broker and also
demonstrates how we can provide a dev environment to working with Kafka with docker and docker-compose.


![Car Location/architecture model](/assets/images/car_position_diagram.png)

The goal is to calculate the position of vehicles inside strategic geojson-polygons and serve the cars and polygons via a REST API.
The implementation consists of Three microservices implemented in Java using Spring Boot and Spring Cloud:

-   `car-position-producer`

-   `car-position-consumer`

-   `car-webservices`


#### Car Position Producer
The purpose of this microservice is to fetch car locations from the third party service then produce an event for each car
and finally, send it to Kafka.

For fetching car locations through an API, which is provided in the form of the public Docker image.
you can use this docker image: car2godeveloper/api-for-coding-challenge 
when you pull and run it for example on port 3000 you can find API swagger specification is available through this URL:
http://localhost:3000/documentation/

For streaming, we should config a scheduler for fetching data periodically. I implement it simply in 
[FetchVehiclesPosition.java](https://github.com/EhsanAsaDev/car-Location-microservice/blob/main/car-position-producer/src/main/java/com/sn/challenge/scheduler/FetchVehiclesPosition.java) file

```java

@Scheduled(fixedDelay =30000 )
public Vehicle[] fetchCarData(){
    log.info("Fetching Vehicles Data .......");
    Vehicle[] vehicles = vehiclesService.getVehicleInfo();
    for (Vehicle vehicle : vehicles){
        log.info("Vehicle with vim:{} sent as event",vehicle.getVin());
        vehicleEventProducer.sendVehicleEvents(vehicle);
    }
    return vehicles;
}
``` 

After fetching data we should send it as an event to Kafka. spring-kafka has a useful and simple class for that. you just need to add this dependency in your pom.

```xml
        <dependency>
            <groupId>org.springframework.kafka</groupId>
            <artifactId>spring-kafka</artifactId>
        </dependency>
``` 

I implemented producing events in [VehicleEventProducer.java](https://github.com/EhsanAsaDev/car-Location-microservice/blob/main/car-position-producer/src/main/java/com/sn/challenge/producer/VehicleEventProducer.java) :

```java

public ListenableFuture<SendResult<String,Vehicle>> sendVehicleEvents(Vehicle vehicle) {

    String key = vehicle.getVin();

    ProducerRecord<String,Vehicle> producerRecord = buildProducerRecord(key, vehicle, "vehicle-position-events");

    ListenableFuture<SendResult<String,Vehicle>> listenableFuture =  kafkaTemplate.send(producerRecord);
    listenableFuture.addCallback(new VehicleEventListenableFutureCallback(key,vehicle));

    return listenableFuture;
}
``` 

The main point here is using Vin as the key of messages. As you know Kafka only guarantees message ordering within a partition so, in this way, we can be sure all data belonging to a specific car will be sent to a specific partition.


#### Car Position Consumer
In our architecture model, the consumer microservice receives messages from Kafka and after finding polygon
saves them in MongoDB. For having a consumer you can simply annotate a method of your service class as a 
listener and just specify the topic name. something like this:

```java

@KafkaListener(topics = {"vehicle-position-events"})
public void onMessage(ConsumerRecord<String, Vehicle> consumerRecord) {
    log.info("ConsumerRecord : {} ", consumerRecord );
    vehicleEventService.processVehicleEvent(consumerRecord);
}
``` 
you can find it in [VehicleEventsConsumer.java ](https://github.com/EhsanAsaDev/car-Location-microservice/blob/main/car-position-consumer/src/main/java/com/sn/challenge/consumer/VehicleEventsConsumer.java)

you should consider that finding polygon is a computational activity so if Kafka bombards with a massive number of car 
locations then we have trouble here in consuming. I will talk about that how we can solve this issue.

#### Car Webservice
This is the simplest one! everything is ready now we only need to expose some rest service and fetch data from 
MongoDB for that. I implement two rest services one of them get a Vin for a car and return location info for this car. 
the second one gets a polygon and show all car now are in that.

```java

@GetMapping(value = "/api/v1//vehiclesByPolygon")
public ResponseEntity<List<Vehicle>> getVehiclesByPolygonId(@RequestParam("polygonId") String polygonId) {

    log.info("REST request to /api/v1//vehicles?polygonId={}", polygonId);
    ResponseEntity<List<Vehicle>> responseEntity = ResponseEntity.status(HttpStatus.OK).body(vehicleService.getVehiclesByPolygonId(polygonId));
    log.info("/api/v1//vehiclesByPolygon?polygonId={} : {}", polygonId, responseEntity);
    return responseEntity;
}

@GetMapping(value = "/api/v1//vehicles")
public ResponseEntity<Vehicle> getVehicleByVin(@RequestParam("vin") String vin) {

    log.info("REST request to /api/v1//vehicles?vin={}", vin);
    ResponseEntity responseEntity = ResponseEntity.status(HttpStatus.OK).body(vehicleService.getVehicleByVin(vin));
    log.info("/api/v1//vehicles?vin={} : {}", vin, responseEntity);
    return responseEntity;
}
``` 

finally, running the project via the home page (http://localhost:8083/) you can access it with swagger to test APIs.


### Testing an Apache Kafka Integration within a Spring Boot Application and JUnit 5

First, you might need to add the spring-kafka-test dependency:

```xml
<dependency>
    <groupId>org.springframework.kafka</groupId>
    <artifactId>spring-kafka-test</artifactId>
    <scope>test</scope>
</dependency>
``` 
next, you should annotate your test class with @EmbeddedKafka and also add @SpringBootTest annotation because we want to 
execute our test inside the Spring context.

you can find it how I configured producer in [CarPositionProducerApplicationTests.java](https://github.com/EhsanAsaDev/car-Location-microservice/blob/main/car-position-producer/src/test/java/com/sn/challenge/CarPositionProducerApplicationTests.java)
and also how I configured consumer in [VehicleEventsConsumerIntegrationTest.java](https://github.com/EhsanAsaDev/car-Location-microservice/blob/main/car-position-consumer/src/test/java/com/sn/challenge/consumer/VehicleEventsConsumerIntegrationTest.java)

*Don't forget for running producer you need to run the docker file it provides for us some car and locations!

### Running project by Local Kafka Setup with Docker and Docker compose
First, you should build and package jar files with Maven.

//mvn clean package

Then,  I provided a docker file for each microservice, so you should create an image for each microservice.

//docker build -t car-position-producer .

//docker build -t car-position-consumer .

//docker build -t car-webservices .

I used bitnami docker image as you can see in [docker-compose.yml](https://github.com/EhsanAsaDev/car-Location-microservice/blob/main/docker-compose.yml) 
take a look at this file:

```YAML
version: '3'

networks:
  kafka-net:
    driver: bridge

services:

  zookeeper-server:
    image: 'bitnami/zookeeper:latest'
    networks:
      - kafka-net
    ports:
      - '2181:2181'
    environment:
      - ALLOW_ANONYMOUS_LOGIN=yes


  kafka-server1:
    image: 'bitnami/kafka:latest'
    networks:
      - kafka-net
    ports:
      - '9092:9092'
      - '29092:29092'
    environment:
      - KAFKA_BROKER_ID:1
      - KAFKA_CFG_ZOOKEEPER_CONNECT=zookeeper-server:2181
      - ALLOW_PLAINTEXT_LISTENER=yes
      - KAFKA_CFG_LISTENER_SECURITY_PROTOCOL_MAP=PLAINTEXT:PLAINTEXT,PLAINTEXT_HOST:PLAINTEXT
      - KAFKA_CFG_LISTENERS=PLAINTEXT://:9092,PLAINTEXT_HOST://:29092
      - KAFKA_CFG_ADVERTISED_LISTENERS=PLAINTEXT://kafka-server1:9092,PLAINTEXT_HOST://kafka-server1:29092
    depends_on:
      - zookeeper-server

  kafka-server2:
    image: 'bitnami/kafka:latest'
    networks:
      - kafka-net
    ports:
      - '9093:9092'
      - '29093:29093'
    environment:
      - KAFKA_BROKER_ID:2
      - KAFKA_CFG_ZOOKEEPER_CONNECT=zookeeper-server:2181
      - ALLOW_PLAINTEXT_LISTENER=yes
      - KAFKA_CFG_LISTENER_SECURITY_PROTOCOL_MAP=PLAINTEXT:PLAINTEXT,PLAINTEXT_HOST:PLAINTEXT
      - KAFKA_CFG_LISTENERS=PLAINTEXT://:9093,PLAINTEXT_HOST://:29093
      - KAFKA_CFG_ADVERTISED_LISTENERS=PLAINTEXT://kafka-server2:9093,PLAINTEXT_HOST://kafka-server2:29093
    depends_on:
      - zookeeper-server


  kafka-server3:
    image: 'bitnami/kafka:latest'
    networks:
      - kafka-net
    ports:
      - '9094:9092'
      - '29094:29094'
    environment:
      - KAFKA_BROKER_ID:3
      - KAFKA_CFG_ZOOKEEPER_CONNECT=zookeeper-server:2181
      - ALLOW_PLAINTEXT_LISTENER=yes
      - KAFKA_CFG_LISTENER_SECURITY_PROTOCOL_MAP=PLAINTEXT:PLAINTEXT,PLAINTEXT_HOST:PLAINTEXT
      - KAFKA_CFG_LISTENERS=PLAINTEXT://:9094,PLAINTEXT_HOST://:29094
      - KAFKA_CFG_ADVERTISED_LISTENERS=PLAINTEXT://kafka-server3:9094,PLAINTEXT_HOST://kafka-server3:29094
    depends_on:
      - zookeeper-server

  docker-mongodb:
    image: mongo:latest
    environment:
      ADVERTISED_HOST: 127.0.0.1
      ADVERTISED_PORT: 27017
    ports:
      - "27017:27017"
    networks:
      - kafka-net


  car-position-server:
    image: car2godeveloper/api-for-coding-challenge:latest
    ports:
      - "3000:3000"
    networks:
      - kafka-net


  car-position-producer:
    image: "car-position-producer"
    ports:
      - "8081:8081"
    networks:
      - kafka-net
    depends_on:
      - kafka-server1
      - kafka-server2
      - kafka-server3
      - car-position-server
    environment:
      - KAFKA_SERVERS=kafka-server1:29092, kafka-server2:29093, kafka-server3:29094
      - CAR-POSITION-SERVER=http://car-position-server:3000

  car-position-consumer:
    image: "car-position-consumer"
    ports:
      - "8082:8082"
    networks:
      - kafka-net
    depends_on:
      - kafka-server1
      - kafka-server2
      - kafka-server3
      - car-position-producer
      - docker-mongodb
    environment:
      - KAFKA_SERVERS=kafka-server1:29092, kafka-server2:29093, kafka-server3:29094
      - CAR-POSITION-SERVER=car-position-server:3000


  car-position-consumer2:
    image: "car-position-consumer"
    ports:
      - "8084:8084"
    networks:
      - kafka-net
    depends_on:
      - kafka-server1
      - kafka-server2
      - kafka-server3
      - car-position-producer
      - docker-mongodb
    environment:
      - KAFKA_SERVERS=kafka-server1:29092, kafka-server2:29093, kafka-server3:29094
      - CAR-POSITION-SERVER=car-position-server:3000

  car-position-consumer3:
    image: "car-position-consumer"
    ports:
      - "8085:8085"
    networks:
      - kafka-net
    depends_on:
      - kafka-server1
      - kafka-server2
      - kafka-server3
      - car-position-producer
      - docker-mongodb
    environment:
      - KAFKA_SERVERS=kafka-server1:29092, kafka-server2:29093, kafka-server3:29094
      - CAR-POSITION-SERVER=car-position-server:3000


  car-webServices:
    image: "car-webservices"
    networks:
      - kafka-net
    ports:
      - "8083:8083"
    depends_on:
      - car-position-consumer
      - docker-mongodb


``` 

Besides this, I configured one instance of car-position-producer and three instances of car-position-consumer. 
As we know if we use a short time in car-position-producer scheduler for fetching data and produce events on Kafka, 
our message broker bombard with many events. The solution is simple we can have multiple instances of consumer,
how much we need!
Finally, by the docker-compose file, you can launch it.

//docker-compose up

Via the home page (http://localhost:8083/) you can access with swagger to test APIs.

