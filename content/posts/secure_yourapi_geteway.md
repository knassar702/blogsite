---
title: "How to Secure your API Gateway"
date: 2022-08-17T20:15:30+02:00
draft: false
toc: false
images:
tags:
  - security
  - microservices
  - zuul
---

Hello Everyone, if you have an API you will need to make a good security
practice for it so we will discuss in this blog about
- How to choose the best option for authentication
- How to make a secure connection between each service 
---
In the beginning, let’s imagine that you have a micro-service and all it does
is that display some information about the user such as the username, job role,
and email address some options are available to us to use to Secure Our API
Gateway, Most of them are usable and we will use them one by one indifferent
cases

* Basic Authentication\
This Protocole is one of the HTTP/1.0 [RFC 1945](https://www.rfc-editor.org/rfc/rfc1945.html#section-11.1) a long time back, this Protoco lets you access your
micro-service using an HTTP header, the user will send the username and password
(base64 encoded) in the header

* Mutual TLS (MTLS)\
Mutual Transport Layer Security is a mechanism by which a client application verifies
a server and the server verifies the client application by exchanging respective certifi-
cates and proving that each one owns the corresponding private keys

* OAuth 2.0\
OAuth 2.0 is the industry-standard protocol for authorization. OAuth 2.0 focuses on client developer simplicity while providing specific authorization flows for web applications, desktop applications, mobile phones, and living room devices. This specification and its extensions are being developed within the IETF OAuth Working Group.


#### >> Choose The Right API authentication Protocole


* Basic Authentication\
    why is this choice is **BAD** for your API?

    - the username and password are long-living credentials\
    if a user provides a username and password to an application, the
    application needs to retain this information for that particular user
    session to access the microservices. The time during which this
    information needs to be retained could be as long as the application
    decide, Therefore if a basic authentication is used, the application has
    to retain this information for long durations of time. The longer this
    information is retained, the higher the chance of compromise. And
    because these credentials rarely change, a compromise of this
    information could have severe consequences. 

    - No restrictions on what the application can do\
    After an application gets access to the
    username and password of a user, it can do everything that the user can do with the
    microservice. In addition to accessing the microservice, the application can do
    anything with those credentials, even on other systems


* Mutual TLS (MTLS)\
    This is a Good Protocole to use, all it does is verify the Cert Key between the server and client, this type of protocoles has a Expire option so this solve the problem that we've with Basic Authentication Header, but this not the best choice for the API (or the end-user), it's like riding a seaplane to walking on the water
    it will work but it would be better if you use a Sea Boat because it's designed for this only and its more faster 
    we will use it later to secure the connection between the API-Gateway and the microservices


* Auth 2.0 (Use ME)\
To understand why OAuth 2.0 is **the best security protocol** for you, first you need to understand your audience.
You need to figure out 
    - who wants access to your resources 
    - for what purpose
    - for how long\
Auth 2.0 provides all of this requirements, so we can use it instead of HTTP/1.0 Basic Authentication or MTLS\
```bash
$ curl -u application1:application1secret \
-H "Content-Type: application/x-www-form-urlencoded" \
-d "grant_type=client_credentials" \
http://localhost:9090/token/oauth/token | jq

{
    "access_token":"47190af1-624c-48a6-988d-f4319d36b7f4",
    "token_type":"bearer",
    "expires_in":3599
}


# Check Permission
$ http://localhost:9090/oauth/check_token -H "Authorization: Basic YXBwbGljYXRpb24xOmFwcGxpY2F0aW9uMXNlY3JldA==" -d "token=626e34e6-002d-4d53-9656-9e06a5e7e0dc&
token_type_hint=access_token" | jq

{
    "active": true,
    "client_id": "application1",
    "scope": "read write",
    "sub": "application1",
    "aud": "http://orders.ecomm.com"
}


```


#### Secure the connection 
in the example at the beginning of the post, we have two microservices one for Authentication and another for Ordering User Data and you already using Auth 2.0 on your application, but we still need to make the last steps to make sure that no one outside the API-Gateway can access any of the microservices


first, we will make a Firewall rule to block any External requests, Also we'll use Mutual TLS (MTLS) to block any Internal requests

![micro_png](/images/micro.svg)


this was a Quick summary of microservices Security in Action Book
follow the RSS to see more posts about this book (Chapter by Chapter)
