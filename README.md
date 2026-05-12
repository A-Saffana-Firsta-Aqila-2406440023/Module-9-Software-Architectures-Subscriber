# Reflection

## 1. What is amqp?
AMQP stands for Advanced Message Queuing Protocol. It is a messaging protocol that enables communication between producers and consumers through a message broker. It supports a separation between an exchange (where the producer sends messages to) and a queue (where the consumer listens from). In the context of the module, AMQP is used via RabbitMQ as the underlying protocol to route messages between the publisher and subscriber services.

## 2. What does it mean? `guest:guest@localhost:5672`, what is the first guest, and what is the second guest, and what is `localhost:5672` is for
In the connection URL `amqp://guest:guest@localhost:5672`, the format follows `username:password@host:port`. 
- The first "guest" is the **username** used to authenticate to the RabbitMQ message broker. 
- The second "guest" is the **password** for that username. "guest/guest" is RabbitMQ's default credential. 
- localhost means the RabbitMQ broker is **running on the same machine as the application**, and 5672 is the default port that RabbitMQ listens on for AMQP connections.

## Simulation Slow Subscriber
![Slow Subscriber Simulation](assets/images/Slow_Subscriber_Simulation.png)

**Why the total number of queue is as such?**

The "Queued messages" chart spiked up to around 15-20 messages because the publisher was run multiple times (5 times in my case) in quick succession, sending 25 messages in total faster than the subscriber could process them. Since the subscriber has a 1s delay (`thread::sleep`) per message, it processes messages slowly one by one, causing unprocessed messages to pile up in the queue. Over time, as the subscriber gradually catches up, the queue count slowly drops back down to 0, as seen in the slow decline on the chart.

## Running Three Subscribers
![Running 3 Subs RabbitMQ](assets/images/Running_3_Subs_RabbitMQ.png)
![Running 3 Subs Console1](assets/images/Running_3_Subs_Console1.png)
![Running 3 Subs Console2](assets/images/Running_3_Subs_Console2.png)
![Running 3 Subs Console3](assets/images/Running_3_Subs_Console3.png)

With 3 subscribers running simultaneously and the publisher run 5 times (in my case), the total of 25 messages sent to the broker were distributed across all 3 subscribers at once. RabbitMQ automatically load-balances the messages, meaning each subscriber only processes a portion of the total messages instead of one subscriber handling all of them. This is why the messages in each console are different, each subscriber receives and processes its own share of the queue. As a result, the queue is drained significantly faster compared to when only 1 subscriber was running, which is visible in the RabbitMQ dashboard where the spike in the "Queued messages" chart is smaller and drops back to 0 much quicker than before. The more subscribers connected to the same queue, the faster the messages are consumed and processed.

**What can be improved?**
- **Improve Error Handling**: Both publisher and subscriber suppress results using `_ =`. If the RabbitMQ connection drops, the queue is unreachable, or the listener fails to bind, errors will be silently ignored. It is better to handle them, log them, or at least use `.unwrap()` or `.expect()` so we know when something fails.
- **Avoid Hardcoded Connection Strings**: The AMQP URL (`"amqp://guest:guest@localhost:5672"`) is hardcoded in both publisher and subscriber. It is best practice to pull this from an environment variable so the code does not need to be changed when deploying to different environments.