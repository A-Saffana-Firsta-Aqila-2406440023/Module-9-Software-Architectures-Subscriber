# Reflection

## 1. What is amqp?
AMQP stands for Advanced Message Queuing Protocol. It is a messaging protocol that enables communication between producers and consumers through a message broker. It supports a separation between an exchange (where the producer sends messages to) and a queue (where the consumer listens from). In the context of the module, AMQP is used via RabbitMQ as the underlying protocol to route messages between the publisher and subscriber services.

## 2. What does it mean? `guest:guest@localhost:5672`, what is the first guest, and what is the second guest, and what is `localhost:5672` is for
In the connection URL `amqp://guest:guest@localhost:5672`, the format follows `username:password@host:port`. 
- The first "guest" is the **username** used to authenticate to the RabbitMQ message broker. 
- The second "guest" is the **password** for that username. "guest/guest" is RabbitMQ's default credential. 
- localhost means the RabbitMQ broker is **running on the same machine as the application**, and 5672 is the default port that RabbitMQ listens on for AMQP connections.