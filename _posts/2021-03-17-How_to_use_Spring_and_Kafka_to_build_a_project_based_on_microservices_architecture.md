---
layout: post
title: How to use Spring boot and Kafka to build a project based on microservices architecture
comments: false
categories: [ Spring boot ]
tags: [Microservices, spring , Kafka]
image: assets/images/car_position_diagram.jpg
excerpt: This is a good example for how Spring boot works with Kafka as a message broker.
In this project I also use MongoDB and Docker. I show you how to write test for producers and consumers
and how use docker compose for configure Kafka in your dev environment ...


---

#### Car Position Producer
The purpose of this microservice is fetch car locations from the third party service then produce an event for each car 
and finally send it to the kafka.

For fetching car locations use API, that is provided in the form of the public Docker image. 
you can use this image car2godeveloper/api-for-coding-challenge docker.
ehwn you pull and run it for example on port 3000 you can find API swagger specification is available under this url:
http://localhost:3000/documentation/

For streaming, we should config a scheduler for fetching data periodically. I implement it simply in FetchVehiclesPosition.java file

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

after fetching data we should send it as event to Kafka. spring-kafka has a useful and simple class 
for that. you just add this dependency in your pom.

```xml
        <dependency>
            <groupId>org.springframework.kafka</groupId>
            <artifactId>spring-kafka</artifactId>
        </dependency>
``` 

I produce event in VehicleEventProducer.java :

```java

public ListenableFuture<SendResult<String,Vehicle>> sendVehicleEvents(Vehicle vehicle) {

        String key = vehicle.getVin();

        ProducerRecord<String,Vehicle> producerRecord = buildProducerRecord(key, vehicle, topic);

        ListenableFuture<SendResult<String,Vehicle>> listenableFuture =  kafkaTemplate.send(producerRecord);
        listenableFuture.addCallback(new VehicleEventListenableFutureCallback(key,vehicle));

        return listenableFuture;
        }
``` 

the main point here is using Vin as the key of the message.AS you know Kafka only guarantees message
ordering within a partition so in this way we can sure all data belong to the one car send to specific partition.


#### Car Position Consumer
In our Architecture model, consumer microservice receive messages from Kafka and after finding polygon save it in 
the MongoDB. For having a consumer you can simply annotate a method of your service class as a listener
and just specify the topic name. something like this:

```java

@KafkaListener(topics = {"vehicle-position-events"})
public void onMessage(ConsumerRecord<String, Vehicle> consumerRecord) {
        log.info("ConsumerRecord : {} ", consumerRecord );
        vehicleEventService.processVehicleEvent(consumerRecord);

        }
``` 

you should consider that finding polygon is a consumption activity so if Kafka bombard with a massive 
number of car locations then we have truble here in consuming. I will talk about that how we can solve this issue.



#### Car Webservice
 this is the simplest one! everything is ready now we only need expose some rest service and
 fetch data from MongoDB for that. I implement tow rest service one of them get a Vin for a car and return 
Location info for this car. second one get a polygon and show all car now are in that.

