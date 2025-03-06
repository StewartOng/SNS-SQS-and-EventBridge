# SNS-SQS-and-EventBridge

First, we explore to develop more in depth understanding of the AWS SQS, AWS SNS and Amazon EventBridge.

AWS SQS (Simple Queue Service)
Amazon Simple Queue Service (SQS) is a fully managed message queuing service that allows you to decouple and scale microservices, distributed systems, and serverless applications. SQS enables you to send, store, and receive messages between software components, without requiring the components to be always available or connected.

Key features of SQS:
- Message Queueing: SQS stores messages in a queue and ensures they are delivered to consumers at least once.
- Decoupling: Helps decouple application components so they don't have to be dependent on one another's availability.
- Two types of queues:
  #1 Standard Queues: Provide “at least once” message delivery and “best-effort ordering”, which means that the order of messages isn't strictly guaranteed, but they are processed.
  #2 “FIFO Queues (First-In-First-Out)”: Guarantee “exactly once” message delivery and “preserve the order” of messages.
- “Scaling”: SQS scales automatically to handle the volume of messages, so you don’t have to worry about managing the infrastructure.
- “Dead Letter Queue (DLQ)”: Messages that can't be processed after multiple attempts can be moved to a DLQ for further inspection.

AWS SNS (Simple Notification Service)
Amazon Simple Notification Service (SNS) is a fully managed service that provides pub/sub (publish/subscribe) messaging and push notifications. It allows you to send messages to multiple recipients or subscribers through various protocols, such as HTTP, HTTPS, email, SMS, and more.

Key features of SNS:
- Pub/Sub Messaging: SNS supports the pub/sub messaging pattern, allowing one message to be sent to multiple subscribers simultaneously. Subscribers can be other services like SQS, Lambda functions, HTTP endpoints, etc.
- Multiple Protocols: You can publish messages to a wide variety of endpoints, including email, SMS, HTTP/HTTPS, Lambda functions, and SQS queues.
- Push Notifications: SNS can be used to send push notifications to mobile devices or distributed services.
- Fan-Out: You can publish a single message to an SNS topic, and SNS will distribute it to all subscribers of that topic.
- Delivery Status: SNS provides feedback on whether messages were successfully delivered to subscribers.
- Dead Letter Queue (DLQ): SNS can send undeliverable messages to an SQS DLQ for troubleshooting and further processing.

AWS EventBridge
Amazon EventBridge is a serverless event bus service that makes it easier to connect applications using data from a variety of sources. It enables you to build event-driven architectures that react to changes in your AWS environment, SaaS applications, and more.

Key features of EventBridge:
- Event-Driven Architecture: EventBridge is designed for building event-driven applications. It captures events from different sources and routes them to target services or resources (like AWS Lambda, Step Functions, SQS, etc.).
- Event Buses: EventBridge uses event buses to receive and route events. It has a “default event bus” for AWS services, and you can also create “custom event buses” for your specific applications.
- Schema Registry: EventBridge offers a schema registry, allowing you to manage event schemas for your event-driven applications, helping with data validation and API consistency.
- Integration with AWS and SaaS: EventBridge can consume events from various AWS services (like AWS Lambda, SQS, etc.) as well as from supported SaaS applications (like Zendesk, Datadog, etc.).
- Routing: EventBridge supports “advanced filtering” and routing of events based on content, so you can direct events to specific targets based on event data.
- Dead Letter Queue (DLQ): EventBridge supports associating a DLQ with the event targets to capture undeliverable events.

 
 Differences Between SQS, SNS, and EventBridge
 (image.png)
 
 

Use Case Examples:
SQS: If you need to process orders in a background system asynchronously, use SQS to queue order messages for processing.
SNS: If you need to send a notification to multiple users or systems (like an email alert when a new user signs up), SNS can publish the message to a topic that all subscribers listen to.
EventBridge: If you want to build an event-driven system where different components (e.g., microservices) react to changes in AWS resources or external events (like updating an inventory after a sale), EventBridge can route these events.

In short, SQS is for queuing and processing, SNS is for broadcasting notifications, and EventBridge is for managing and routing events in a decoupled, event-driven architecture.

Q1: Does AWS SNS guarantee exactly once delivery to subscribers?
AWS Simple Notification Service (SNS) does not guarantee exactly once delivery. SNS provides at least once delivery. This means that a message may be delivered multiple times to subscribers under certain circumstances, such as network issues, retries, or other failures in the delivery process. Therefore, subscribers should be idempotent and capable of handling duplicate messages.

Q2: What is the number of the dead-letter queue? Is this a feature available to AWS SQS/SNS/EventBridge?
Dead-letter queues (DLQs) are a feature in AWS SQS and SNS, but not EventBridge. The number of the dead-letter queue isn't fixed but is instead associated with each individual SNS topic or SQS queue.
•	AWS SQS: You can configure a DLQ for an SQS queue. If a message cannot be processed successfully after several attempts, it is moved to the associated DLQ.
•	AWS SNS: For SNS, you can configure a DLQ for SNS subscriptions (e.g., SQS queues) to capture undeliverable messages.
•	AWS EventBridge: EventBridge doesn't have a native dead-letter queue (DLQ) feature in the same way as SNS and SQS, but you can still configure DLQs for EventBridge targets like Lambda functions or Step Functions by associating them with SQS queues.

Q3: How would you enable a notification to your email when messages are added to the AWS Dead Letter Queue (DLQ)?
To enable an email notification when messages are added to an AWS DLQ (e.g., for SQS), follow these steps:
1.	Create an SNS topic:
o	Go to the SNS console and create a new SNS topic.
o	Subscribe your email address to the SNS topic.
2.	Set up an SQS DLQ (if not already done):
o	If your primary SQS queue has a DLQ, configure the DLQ in the SQS console.
3.	Create a CloudWatch Alarm for DLQ:
o	Go to the CloudWatch console.
o	Create an alarm based on the number of messages in your DLQ SQS queue. 
	Under "Create Alarm," select "SQS" as the metric and choose the DLQ queue.
	Set a condition (e.g., when the "ApproximateNumberOfMessagesVisible" metric is greater than 0).
4.	Set up SNS notification for the CloudWatch alarm:
o	In the CloudWatch alarm settings, configure the action to send a notification to the SNS topic you created earlier.
5.	Confirm email subscription:
o	After configuring the alarm, you’ll receive an email asking you to confirm the subscription. Confirm it, and you will get an email notification when the number of messages in the DLQ exceeds your specified threshold.
This setup will allow you to be notified by email whenever messages are added to the DLQ.

References
[1] AWS re:Invent 2020: Scalable serverless event-driven architectures with SNS, SQS & Lambda

[2] Choosing Events, Queues, Topics, and Streams in Your Serverless Application - AWS Online Tech Talks

[3] AWS re:Invent 2024 - Best practices for serverless developers (SVS401)

[4] (61) AWS re:Invent 2024 - Best practices for serverless developers (SVS401) - YouTube 

[5] AWS SQS vs SNS vs EventBridge - When to Use What?

[6] AWS re:Invent 2024 - Accelerate serverless deployments using Terraform with proven patterns (SVS320)

[7] AWS re:Invent 2024 - AWS Lambda and Apache Kafka for real-time data processing applications (SVS321)

[8] Accelerate serverless deployments using HashiCorp Terraform in AWS

[9] Automate Serverless Architecture AWS S3 and Lambda with Terraform Cloud & GitHub Actions | Hands-on

