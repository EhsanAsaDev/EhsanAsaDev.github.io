---
layout: post
title:  "Stay on the Microservices Path"
comments: false
categories: [ Software Craft ]
tags: [Microservices, Monolithic ]
image: assets/images/stay_on_the_microservices_path.jfif
excerpt: Microservices Architecture is a popular approach these days. As a developer we always desire to work with up to date 
         framework and tools, but most of the time we don't consider all things together we choose microservices as our 
         Architecture, but we think and behave in a monolith way Involuntary. In this article I want to review some popular 
         mistakes we made when we wanted to work in microservices.
---

#### Introduction
Microservices Architecture is a popular approach these days. As a developer we always desire to work with up to date 
framework and tools, but most of the time we don't consider all things together we choose microservices as our 
Architecture, but we think and behave in a monolith way Involuntary. In this article I want to review some popular 
mistakes we made when we wanted to work in microservices.

#### Independent in development
For developers this is the most tempting thing when you work in microservices architecture. Independent in development 
does not mean you should use every framework in your tech stack! The best practice is using limited frameworks for each 
microservice and if and only if we have a special requirement in any microservices we can use something new for covering 
our requirement. Beside that if one day we want to improve or change the tech stack we can use this feature of microservices 
architecture. We want each team to work independently but this doesn't mean developers shouldn't have communication with 
each other. Having technical interaction between different teams and projects is better than for example having a 
separate role like product owner for interacting.

#### Be serious about isolation
Microservices that share a database are just a distributed monolith. If you share your database you should be ready for 
consequences. Each change in database for one microservice requirement may cause other microservices to crash. Instead 
of sharing databases for using the same table, prepare some services. In this way when one team has some changes other 
teams can be aware of changes automatically.

#### Don't act like Monolith
![Don't act like Monolith](/assets/images/dont_act_like_monolith.png)

Be careful when using microservices architecture you don't act like monolith. for example if you indicate to have 
independent services you should have an independent release plan. Another things you should be careful is don't combine 
disadvantages of microservices with disadvantages of monolith for example if it's possible you had some issue in 
authorization services when there is high terrific in monolithic application, it can happen simply in microservices and 
can be cause of crash other services. So have a solution for them using a circuit breaker, load balancer , …


####  Invest in CI/CD
Microservice Architecture without well CI/CD is a disaster. Instead of having separate DevOps guys or teams for each 
microservice, use more automation. You should indicate that every change that passes all stages of your pipeline, merges 
to the main branch firstly, then releases to your customers quickly without any human intervention.


####  Conclusion
Microservices is a way of thinking and working not just how to code. So we should be care and think more especially 
when we have a problem, we shouldn't try to solve our problem in a monolithic way.
