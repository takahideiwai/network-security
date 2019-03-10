---
title: "Introduction"
teaching: 10
exercises: 0
questions:
- "What is network security?"
- "What are some types of network security attacks?"
- "What are the broader impacts of network security issues?"
objectives:
- "Describe man-in-the-middle attacks"
- "Describe various vulnerabilities in computer networks"
keypoints:
- "Network security affects all modern communication!"
---

## Network Security

Network security is primarily concerned with unauthorized access, denial, or, malicious modification 
of network communication. Network security attacks can involve *passive* exploits such as *eavsdropping* or 
*wiretapping*, where a hacker can monitor all communication on a network and access privileged information. Other 
attacks such as a *man-in-the-middle* attack involve a hacker inserting themselves in between two communicating 
parties and intercepting and possibly altering the communication between them without their knowledge. With the 
centrality of computers and network communication in activities such as banking, commerce, social media, and communication 
to name a few; the ability to intercept and alter network traffic can have far-reaching consequences. Hackers 
can intercept authentication information, privileged information such as credit card and social security data, 
and use this information for nefarious purposes. 

### Man-in-the-middle Attack

In a man-in-the-middle (MITM) attack, a hacker has successfully intercepted communication between two parties, without 
their knowledge. Each of the affected parties assumes they are directly communicating with their intended counterpart, but 
are instead communicating with the hacker who is relaying possibly modified traffic between the parties. With the ability 
to modify communication, a hacker can insert malicious links and code into web content being transmitted from a server to 
a client, turn off SSL communication by transforming HTTPS links into HTTP, and more. 

In the next lesson, we will learn about a particular MITM attack, **ARP Poisoning**.

{% include links.md %}

