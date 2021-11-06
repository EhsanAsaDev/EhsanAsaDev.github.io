---
layout: post
title: An Overview of Azure Integration Services based on a practical scenario
comments: false
categories: [ Azure ]
tags: [Azure Integration Services, Logic Apps , Service Bus, API Management]
image: assets/images/entreprise-solution-Azure.png
excerpt: In this post, I'm going to define a practical scenario for integrating systems 
         and then answer "How Azure integration services can help us for design a solution for 
         scenarios like this?"
         

---

### Azure Integration Services
Before writing this post I spent some time reviewing how there are topics about Azure integration service on the web.
I read some blog posts and also the official page of the Azure integration service.
I remembered how these explanations were not easy for me to understand when I wanted to start working on the Azure integration service.
so first I want to define a realistic problem and then explain how we can use Azure's features to solve our problem.
I like to say that I don't want to explain how you should exactly do some things on Azure, 
I mean there is no something like you should click on this or that and then, 
anyway my goal is to give you a meaningful big picture about Azure integration services features.

#### Scenario:
Suppose that we want to implement a data integration between one source and multiple destinations.
For example, when the price of one good changes in our source we want to notify this change to each 
destination.
The source and each destination have some rest services, so we should use these web services to 
catch changes from our source and apply them to each destination.
If I want to implement a solution for this scenario from a scratch, possibly first I wrap each 
web service of the system as a new microservices and then use one queue and topic provider 
like Apache Kafka, because I want to my microservices work independently and asynchronously. 
In this way, I can have a scheduler on the source side for getting changes and publish
them to a topic, and have multiple subscribers for trigger and catch changes and apply them on destinations.

### How Azure integration services can help us for scenarios like this?

#####Logic Apps
Logic Apps is designed to implement some processes. They help us define our workflows.
There are many connectors (more than 200) and actions are accessible on logic apps.
creating a logic app typically requires writing no code at all. Instead, developers use the Logic Apps Designer, 
a visual tool, to create new processes.

![Logic_App_catch_data_from_source](/assets/images/Logic_App_catch_data_from_source.png)

Look at the above picture again! does not familiar to you? It is a simple version we exactly need to 
do for fetching changes from the source.

As this example shows, the Logic App Designer lets its users create workflows by arranging actions 
on a surface. Like all logic apps, this one starts with a trigger, which in this case indicates that 
the workflow should run whenever our scheduler is triggered. That is configurable and there are many 
options, I configed it for triggering every 24 hours. It then calls a rest API, we can determine URL, 
request body, etc.
After this, it validates the response body of the rest call and builds a JSON object.
Then executes a loop and iterates on the JSON object. we assume that we received a list of changes, 
so it executes a conditional for each change object: If the status property is true, the logic app 
also sends a change object as a new message to a queue. Before using the service bus you should 
config that.
we will talk about the service bus later.

I hope that it illustrates the basics of how a developer creates a logic app. The key point is that 
writing code isn’t required, letting developers implement business processes quickly and easily.
Logic Apps are at the heart of Azure’s iPaaS offering, letting you easily connect the software and 
services you need to access. And because it allows you to integrate disparate services running both 
in the cloud
and on-premises, Logic Apps provides an effective workflow solution for the world today.

##### Service Bus
The essence of application integration is software talking to other software. But how should 
this communication happen? Instead of sending a message to a queue, we can call the destination API here.
In other cases, though, this synchronous style of communication won’t work.
What if both applications are not available at the same time, for instance?
For situations like this, an asynchronous approach is required.
This kind of communication is exactly what Service Bus provides. Because it lets applications 
(Logic Apps) exchange messages through queues, Service Bus allows non-blocking interactions between 
different chunks of software.

Azure Storage Account indeed offers the option to create queues, but this should be used for simple integrations and specific scenarios.
The storage queues, feature a simple REST-based GET/PUT/PEEK interface, providing reliable, persistent messaging within and between services.
On the other hand, the service bus goes further, supporting publish and subscribe, as well as advanced integration patterns, with the option of multiple communication protocols, data contracts, trust domains, and network environments.

The usage of topics is handy for systems that process messages from several sources and must provide a feedback response.

##### Functions
Supposed that when we catch changes object we should transform this object to a new form and then apply it to our destinations. if this translation is complicated may it's better we write code for that.
For these cases, Azure has a serverless solution called Azure Functions.
Azure Functions, let developers focus entirely on writing code. All the
computing infrastructure they rely on—virtual machines (VMs), support for scalability, and more—is managed for them. Because of this, creating applications gets faster and easier. And running
those applications often gets cheaper, because you’re charged only for the compute resources your code actually uses.
Azure Functions allows you to write custom nano-services in supported languages such as Java, C#, F#, and Javascript.
You can then use Logic Apps to call those functions.

![Logic_App_transform_object](/assets/images/Logic_App_transform_object.png)

Like all logic apps, this one starts with a trigger, which in this case indicates that the workflow should run whenever any messages push to the queue. This logic app uses a Scope structure.
you can use Scopes when you want to implement exception and error handling.
In The Scope, logic apps try to call a function and then and send the result as a new message to another topic.
Thanks to Scope if there is any exception accrued in this group of actions then it catches and send to Azure log analytics.

Azure Log Analytics is a platform that helps us to aggregate VM and Azure resource log files into
a single data lake (called a Log Analytics workspace) and then run queries against the data.

##### Database as a service
Don’t think of Azure Integration Services as an isolated, standalone iPaaS, it’s not. Instead, it’s part of the larger Azure offering, giving you immediate, integrated access to any other cloud services you need. if you want to store unstructured data in Azure you have multiples option for that.
for example, you can use Cosmos DB service and also use some components on Logic Apps to connect and fetch data
from your Cosmos DB.

![Logic_App_transform_object](/assets/images/Logic_App_post_to_destination.png)

We should complete our solution with the last logic app. It is triggered when a message is received on a topic.
Then call a rest service on a destination and finally for example for reporting save transformed object as a document on CosmosDB.


##### Azure API Management
You know API management gives enterprises greater flexibility when reusing the functionality of API integrations
and helps save time and money without trading off security.
Azure API Management offers a centralized way to manage and control the catalog of APIs to access the backend systems
for driven integration, enabling internal systems, partners, and businesses to connect.

som of capabilities of Azure API Management provides:
- securing APIs with authentication and authorisation methods such as Azure AD, plus use of client certificates
- managing consumer accounts
- creating mock responses
- cashing

![Logic_App_transform_object](/assets/images/Azure_API_Management.png)


##### ARM template
All things in Azure integration service such as logic apps expressed in JavaScript Object Notation (JSON) called Azure Resource Manager.
for example for logic apps This helps it fit well into existing DevOps environments, each one is just a text file, and it’s even possible to create a new logic app workflow directly in JSON.
In this way, we can maintain multiple versions of our logic apps in version control, and we can simply deploy them
from one place to another place.

This is the JSON of the last logic app:
```json
{
  "definition": {
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "actions": {
      "Comos_DB:_Create_document": {
        "inputs": {
          "body": "@body('HTTP:_call_rest_service_from_destination')",
          "host": {
            "connection": {
              "name": "@parameters('$connections')['documentdb']['connectionId']"
            }
          },
          "method": "post",
          "path": "/v2/cosmosdb/@{encodeURIComponent('AccountNameFromSettings')}/dbs/@{encodeURIComponent('ToDoList')}/colls/@{encodeURIComponent('Items')}/docs"
        },
        "runAfter": {
          "HTTP:_call_rest_service_from_destination": [
            "Succeeded"
          ]
        },
        "type": "ApiConnection"
      },
      "HTTP:_call_rest_service_from_destination": {
        "inputs": {
          "method": "POST",
          "uri": "https://reqres.in/api/users"
        },
        "runAfter": {},
        "type": "Http"
      }
    },
    "contentVersion": "1.0.0.0",
    "outputs": {},
    "parameters": {
      "$connections": {
        "defaultValue": {},
        "type": "Object"
      }
    },
    "triggers": {
      "Service_bus:_When_a_message_is_received_in_a_queue_(auto-complete)": {
        "evaluatedRecurrence": {
          "frequency": "Minute",
          "interval": 3
        },
        "inputs": {
          "host": {
            "connection": {
              "name": "@parameters('$connections')['servicebus']['connectionId']"
            }
          },
          "method": "get",
          "path": "/@{encodeURIComponent(encodeURIComponent(''))}/messages/head",
          "queries": {
            "queueType": "Main"
          }
        },
        "recurrence": {
          "frequency": "Minute",
          "interval": 3
        },
        "type": "ApiConnection"
      }
    }
  },
  "parameters": {
    "$connections": {
      "value": {
        "documentdb": {
          "connectionId": "/subscriptions/7b722d59-9938-40ce-ae9f-90b7ce9d49c6/resourceGroups/MHCDEV-integration-subscription-to-wayfair/providers/Microsoft.Web/connections/documentdb",
          "connectionName": "documentdb",
          "id": "/subscriptions/7b722d59-9938-40ce-ae9f-90b7ce9d49c6/providers/Microsoft.Web/locations/canadacentral/managedApis/documentdb"
        },
        "servicebus": {
          "connectionId": "/subscriptions/7b722d59-9938-40ce-ae9f-90b7ce9d49c6/resourceGroups/MHCDEV-integration-subscription-to-wayfair/providers/Microsoft.Web/connections/servicebus-1",
          "connectionName": "servicebus-1",
          "id": "/subscriptions/7b722d59-9938-40ce-ae9f-90b7ce9d49c6/providers/Microsoft.Web/locations/canadacentral/managedApis/servicebus"
        }
      }
    }
  }
}
```

As you can see everything define with JSON. It contains names, parameters, etc.
After you are familiar with the ARM template instead of working with Logic App Designer maybe you prefer to do some 
changes directly on JSON. 


#### Conclusion

Azure Integration Services provides components that work together fine on a reliable and reusable platform, 
so we don’t need to design integration all the time from scratch, reducing the risk of implementation 
taking advantage of a widely used platform.















