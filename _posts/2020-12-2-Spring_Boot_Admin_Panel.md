---
layout: post
title:  "Spring Boot Admin Panel"
comments: false
categories: [ Spring boot ]
tags: [Microservices, spring cloud, Admin]
image: assets/images/spring_boot_admin_panel.png
---

If you work with spring boot and microservices and need admin UI for your services you should definitely try 
codecentric's Spring Boot Admin. It's a powerful community project that provides an admin interface for Spring Boot applications.

Add dependency to your pom file:
```xml
<dependency>
    <groupId>de.codecentric</groupId>
    <artifactId>spring-boot-admin-starter-server</artifactId>
    <version>2.3.0</version>
</dependency> 
```
I suggest that if you have discovery server you use this too as admin panel:
```YML
spring:
  application:
    name: eureka-server
  boot:
    admin:
      context-path: /admin  #A different context path for Spring boot admin
```

And finally annotate your main class 
```java
@SpringBootApplication
@EnableEurekaServer
@EnableAdminServer
public class EurekaServerApplication {

    public static void main(String[] args) {
        SpringApplication.run(EurekaServerApplication.class, args);
    }

}
```

good to know: <a target="_blank" href="https://www.linkedin.com/in/ACoAAC7yTmcBN7X675yvg0H3zGBjrab72zU1U4o?lipi=urn%3Ali%3Apage%3Ad_flagship3_detail_base%3Biuoo%2BQVwR0GB60spOV5QSQ%3D%3D">Johannes Edmeier</a>
 was the maintainer of the Spring Boot Admin project. He could do this, because his company allows him 
to work %25 of his work time on this project but It's not enough and he invested more of his private time on this 
project with passion. This project has been completed about 4.5 years ago and now has over 9.3k stars on GitHub. 
you can use it for free and enjoy it like me. 