# Envio de Mensagem Usando a Configuração Básica do RabbitMQ

![Java](https://img.shields.io/badge/java-%23ED8B00.svg?style=for-the-badge&logo=openjdk&logoColor=white)
![Spring](https://img.shields.io/badge/spring-%236DB33F.svg?style=for-the-badge&logo=spring&logoColor=white)
![RabbitMQ](https://img.shields.io/badge/RabbitMQ-FF6600?style=for-the-badge&logo=rabbitmq&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![Postman](https://img.shields.io/badge/Postman-FF6C37?style=for-the-badge&logo=postman&logoColor=white)
![Maven](https://img.shields.io/badge/Maven-C71A36?style=for-the-badge&logo=apache-maven&logoColor=white)


This project aims to demonstrate, in a simple way, the communication between two applications using RabbitMQ — one application acts as the message sender and the other as the receiver (Publish e Subscriber).

The following technologies were used as the foundation: Java, Spring Framework, Docker, Postman, Maven, and Lombok.

While other tools are involved, the main focus of the project is to understand the concepts and functionality of RabbitMQ.

## Table of Contents da Publisher

- [Controller](#controller)
- [Service](#service)
- [Application Properties](#application-properties)
- [RabbitMQ com Docker](#rabbitmq-com-docker)
- [Getting to know QUEUE](#getting-to-know-queue)



## Controller

1. Class Controller:

```
package com.rmq.example.publisher.controller;

import com.rmq.example.publisher.service.PublisherService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class Publisher {

    @Value("${rabbitmq.queuename}")
    private String queueName;

 @Autowired
private PublisherService publisherService;

    @PostMapping("/publish/text")
    public void publishText(@RequestBody String text){

        System.out.println("Sending Menssage" + text);
        publisherService.publishTextMessage(text,queueName);


    }
}

```

Note: The rabbitmq.queuename is what I register in rabbitMQ to receive and send messages. It is important that the name used is present both when sending and receiving.


## Service

```
package com.rmq.example.publisher.service;

import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class PublisherService {

    @Autowired
    private RabbitTemplate rabbitTemplate;

public void publishTextMessage(String message, String queueName){
System.out.println(message);
rabbitTemplate.convertAndSend(queueName,message);
}

}

```

## Application Properties

```
application.properties:
spring.application.name=publisher
server.port=8081
rabbitmq.queuename=MY_TEST_QUEUE

```

## RabbitMQ com Docker

Run a RabbitMQ Management UI Docker image with the following command:

```
docker run -d --name rabbitmq -p 5672:5672 -p 15672:15672 rabbitmq:3-management
```
-d: Runs the container in detached mode (in the background).

--name rabbitmq: Assigns the name rabbitmq to your container.

-p 5672:5672: Maps the standard AMQP port (5672) from the container to your host.

-p 15672:15672: Maps the RabbitMQ management UI port (15672) from the container to your host.


## Getting to know QUEUE

👉 http://localhost:15672

👤 User: guest

🔐 Password: guest

Register the queue that is in my application.properties

Queues and Streams:

The queue in RabbitMQ is the central component for storing and delivering messages. It works like an inbox: messages are sent by producers (publishers) and consumed by consumers (subscribers).

<p align="center">
    <img src="./.github/image/postmanenvio.png" width="500px">
    </p>

After sending, we can follow the RabbitMQ overview screen to monitor the status and behavior of the queue in real time. It is extremely useful for developers and administrators to understand how messages are being handled.

<p align="center">
    <img src="./.github/image/acompanhamento%20dashboard%20rabbitMQ.png" width="500px">
    </p>

On the Application side we can observe the sending and receiving:

Publisher:

```
2025-06-07T20:27:11.687-03:00  INFO 19224 --- [publisher] [nio-8081-exec-4] o.s.web.servlet.DispatcherServlet        : Completed initialization in 1 ms
Sending Menssage"envio de mensagem"
"envio de mensagem"
2025-06-07T20:27:11.775-03:00  INFO 19224 --- [publisher] [nio-8081-exec-4] o.s.a.r.c.CachingConnectionFactory       : Attempting to connect to: [localhost:5672]
2025-06-07T20:27:11.846-03:00  INFO 19224 --- [publisher] [nio-8081-exec-4] o.s.a.r.c.CachingConnectionFactory       : Created new connection: rabbitConnectionFactory#4a163575:0/SimpleConnection@5777cd39 [delegate=amqp://guest@127.0.0.1:5672/, localPort=65413]
```

Subscriber:
```
Body:'"envio de mensagem"' MessageProperties [headers={}, contentType=text/plain, contentEncoding=UTF-8, contentLength=0, receivedDeliveryMode=PERSISTENT, priority=0, redelivered=false, receivedExchange=, receivedRoutingKey=MY_TEST_QUEUE, deliveryTag=1, consumerTag=amq.ctag-N4Aut2RC9E9VqPoSuHBWRA, consumerQueue=MY_TEST_QUEUE])
```
