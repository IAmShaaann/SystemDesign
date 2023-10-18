### What is Message Queue?

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
