### Publish-Subscribe Pattern VS Message Queue.

---

**What is a Message Queue?**

A message queue is a technology service, that works on the idea that in a distributed system every task that is pushed on a queue will only be served once. and once that is served it will be popped from the queue. So, all the incoming request are stored in a queue and are served asynchronously from the queue.

**When to use message queues and do you really need them?**

Suppose you are working at a Pizza Shop. there are 2 people one makes the orders and one takes the orders. So, when a customer comes in employee1 takes the order ans 2 makes them.

At the same time if multiple customer come in. so the employee1 will keep taking the orders and maintain a list of orders they have to send. this way employee2 will have only one job of making pizzas.

This is somewhat a message queue. So, if you request to a web server and the server is engaged serving some other client. it will send you an acknowledgement that yes your request has been received and response will be generated shortly.

This entire process is Async so you wont have to wait for one task to get completed before starting a new one. they can all happen simultaneously.

**Now the question is how do you achieve this?**

Okay so lets say you have multiple pizza shops now S1 | S2 | S3 | S4 , and now you can not keep an in memory list to keep track of all the orders assuming each pizza shop has its own server. So we will do is that we will keep a common DB. and have all the servers access that queue from there. So if have multiple servers they will take the request and dump them in the shared DB.

**What happens if one of the server crashes?**

In case of let’s say S4 crashes and it was handling customer C10, C32. so now these customer’s pizzas will no more be made. so we will need to distribute this load onto other available servers/pizza shops. So you have reroute the request that were coming to S4 to rest of the servers so that we dont loose our customers.

**Now, How will you know that one of the servers has crashed?**

For these we will introduce a notifier. which will sit between the servers and the DB. the notifier will send heartbeat to all the servers every 10 seconds and the servers will respond with an acknowledgement as if a server misses to acknowledge a heartbeat the notifier will consider it as a dead server and using the load balancer we will redirect those requests to other servers.

Now when you want all these functionalities combined you can use a **Message/Task Queue.** Which will basically take in all your requests. segregate them, assign them to the particular servers. verify in the server are running or not. If not then it will reroute the request to the next server.

### Request-Response Model

It’s a simple model, where a client makes a GET request to the server and the server returns a response to the client. Which can be some text, html/Css files etc.

### Where it breaks?

Lets take YouTube - Suppose you have these services.

Upload Service ⇒ Compress Service ⇒ Format Service ⇒ Notification Service

Each Service has their own unique tasks.

So when a user uploads a video. the video first goes to the Upload Service ⇒ Compress Service ⇒ Format Service ⇒ Notification Service. And once the notification service is done notifying the subscriber about their favourite YouTubers video. it sends an acknowledgement to Format Service then to ⇒ Compress Service then to ⇒ Upload Service and then the user is notified that the video has been uploaded.

So this is how it works -

User ↔ Upload Service ↔ Compress Service ↔ Format Service ↔ Notification Service

The drawback of this is that each service basically is blocked till the next service complete its execution. and if any of the service fails to deliver its supposed task the entire cycle will break.

**Pros -**

1. Elegant and simple.
2. Scalable

**Cons -**

1. Bad for multiple Receivers.
2. High Coupling - (Dependencies of components over one another)
3. Chaining. Where the response on one service depends upon another service.
4. The Service will keep running till the client is live.

**Publish-Subscribe Architecture →**

Now unlike the Message Queues, A pub-sub is an architecture where the publishers makes a request or message and all the subscribers are received with the same message.

**Example 1 -**

So lets say Youtube. You upload a video and YouTube has multiple services that get triggered when the video is uploaded.

Upload Service | Compress Service | Format Service | Notification Service

Pub-Sub Architecture makes sure that all the services will get the same copy of the original message that is the original video.

Unlike message queue, where when on task is processed it is popped from the queue and then no other user/service can access it.

**How does it work?**

We have these services - Upload Service | Compress Service | Format Service | Notification Service and channel/queue.

When a client uploads a video a TCP connection is established. with the channels. the channel receives the Raw MP4 Video. the connection is closed and now the client’s TCP is no more blocked.

Now the channel has the MP4 Video (Publish). All the services that are subscribed to the channel can now access the MP4 video. but they will follow an async mechanism.

1. Compress service takes the MP4 and compresses it. and pushes the compressed version to the channel.
2. Now the Format service takes the compressed video and formats it into, 420p, 720p, 1080P and pushes those formatted videos or their acknowledgement to the channel.
3. Once the notification service see the acknowledgement for the 1080p video is sends the notification to the subscribers.

Now lets say you want to add a Copyright Service that tracks the video copyright. you can just add the service to the appropriate place and everything is good. Which was not possible in the Request Response Architecture. there you will have to define a place and make connection for the services from where it will take the data as request and send response. also making the client TCP connection blocked By the time the video is still uploading.

**Pros -**

1. Scales very well with multiple services/receivers.
2. Great solution for micro service architectures.
3. loosely coupled. (Less dependency of services over one another)
4. Does not have dependency on the client being active.

**Cons -**

1. Message delivery issues (You have no way to find out that the publishers has published the message. and the subscribers have subscribed to the message)
2. Complexity

**Example 2 -**

Client ⇒ makes a request to S1 ⇒ S1 makes a request to S2 && S0. ⇒ S2 makes request to S3 & S4.

Now how will this proceed if we are working with an async architecture. S1 makes request and waits for response from S0 and S2. Now S2 makes calls further to S3 and S4. by the time S0 has completed its execution and returned a response. And then S3 and S4 complete the execution. the the response is send to S2. who now has response from S0 and S3&4 and now the response is send to S1 and from there to the client.

Pretty complicated.

Now what if S4 fails to execute its task. a failure response will be send to S2 and then S1 and then clients. Highly Coupled System.

Let’s say the execution of S2 was completed and it stores the data in the DB. but later S0 crashed. Now there is a dunder data in the S2 DB. which is of no use as the query never completed. Data Inconsistency.
