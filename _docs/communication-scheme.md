---
title: Communication scheme
permalink: /docs/communication-scheme/
author: 'Carmela Greco'
---

__JMS, Java Messaging Service__
The project implements the consensus algorithm together with the fault detection. The communication scheme is made up by a set of session and message driven Enterprise JavaBean (EJB). They communicate though the enterprise messaging API, JMS: Java Message Service.  

In this peer-to-peer messaging system, a client can communicate with other clients, by sending and receiving messages on apposite resources. 
Peers are loosely coupled, sender and receiver only know what kind of message will be exchanged and what is its destination. 
Peers can communicate in synchornous and asynchronous way. 

JMS supports 2 messaging models: 
1. __point-to-point__
2. __publish-subscribe__
Ognuna delle due è relativa a una risorsa differente definita su JSM;
1. __queue__
2. __topic__
   
In the first case messages will be forwarded to a queue, in the second case there is a broadcast/multicast communication.

#### __Messaging on Topic1__
On Topic1 messaging achieves the fault detection. Peers have some session beans dedicate to the fault detection and exchange periodically some "TextMessage" messages that contain the message "is alive". In this way the peer sends its heart-beat.
Each peer receives the message "is alive° through the fault detection modules and updates its session bean accordingly.

![Topic 1](../images/Topic1.png)

#### __Messaging on Topic2__
On Topic 2 messaging achieves the consensus algorithm. Peers exchange "ObjectMessage" objects. They contain a "ConsensusMessage" class instance that counts the round number, the sender id and an hashtable with the proposed values.

![Topic 2](../images/Topic2.png)