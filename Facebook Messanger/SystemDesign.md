# How would you design Facebook Messenger ?

## Functional Requirements -

1. Real time messaging.
2. Being able to create groups.
3. Show online status. - User is online or not.
4. Image/Video uploads.
5. Read Receipts
6. Push Notifications.

## Technical Requirements -

1. Low latency.
2. High Volume & Availability
3. Reliable
4. Secure

**Thought process** -

Chat application works basically like this. We have 2 users. One will be the sender and another will be the receiver. In order for 2 users to establish a connection directly there is no efficient and secure way. So what we can do is we can have a server where each user will send data.

Now, once the users send messages how will the server let others know that there is a message for them? There are multiple ways.

1. The user can ask the server every 5 seconds if there is a message for them? if yes then get the message. Not very feasible if we are working with very large volume of users.
2. We can have an open connection between user and the server but then again not much feasible for a large number of users.
3. Web Sockets → WebSockets basically sends the client with an acknowledgement that they have a message. So we can have one chat API server where every client will send messages and then Chat server will let the intended client know that there is a message for them.

**Database Schema**

1. User
   1. id - Primary Key
   2. username - string
   3. last_active - timestamp
   4. email - string
2. Message
   1. id - Primary Key,
   2. text - string
   3. media_url - string
   4. message_to - User
   5. message_from - User
3. Conversation
   1. id - Primary Key
   2. channel - [User]
4. user_conversation -
   1. user - User
   2. conversation - Conversation
