# What is a rate limiter?

A rate limiter is a service that prevents user requests to a server within given period of time.
So if a user has requested for more that N number of requests with T time. The rate limiter will
prevent the user to make further request for Q time.
Questions -

1. Do we want to implement a server side rate limiter? YES.
2. How to distinguish users? (IP Address)
3. Let the user know that they have throttled the request count. (429 Error Code)
4. Different methods for rate limiting -
   a. **Token Bucket** - This method basically works like a bucket. so a bucket has bunch of tokens.
   and as soon as the user makes a request a token is associated with that request.
   and with each request a token is assigned. So, as soon as all the tokens are used from the
   bucket the server can send a 429 error saying the user has hit the throttle.
   b. **Time Window** - With this method we predefine the request count with T unit of time.
   So lets say a user can make 30 request every 10 seconds so as soon as the user hits the 30
   request mark with 10 second the 429 error code is sent from the server to the user. and as
   soon as those 10 seconds get completed the new time window is allocated to the user and they
   can start making new requests.
