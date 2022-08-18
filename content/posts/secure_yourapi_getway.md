---
title: "How to Secure your API Getway"
date: 2022-08-17T20:15:30+02:00
draft: true
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
    This is a Good Protocole to use but is not the BEST choice for the API, it's like riding a seaplane to walking on the water
    it will work but it would be better if you use a Sea Boat because it's designed for this only and its more faster 


* Auth 2.0 (Use ME)\
To understand why OAuth 2.0 is **the best security protocol** for you, first you need to understand your audience.
You need to figure out 
    - who wants access to your resources 
    - for what purpose
    - for how long


![micro_png](/images/micro.svg)
