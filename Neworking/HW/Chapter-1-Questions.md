## Review Problems
1, 7, 8, 9, 11, 13, 17 (several correct answers) 18, 19, 21, 23-25
1. 1. What is the difference between a host and an end system? List several differ- ent types of end systems. Is a Web server an end system?
	No difference. Yes a Web server is a system IF it's on the edge of the internet (google server is not)
7. What is the transmission rate of Ethernet LANs?
	10Mbps, 100Mbps, 1000Mbps. 10GBps
9. HFC, DSL, and FTTH are all used for residential access. For each of these access technologies, provide a range of transmission rates and comment on whether the transmission rate is shared or dedicated.
	- Speeds are in notes
	1. FTTH Not shared, DSL & HFC Shared
11. Suppose there is exactly one packet switch between a sending host and a receiving host. The transmission rates between the sending host and the switch and between the switch and the receiving host are R1 and R2, respectively. Assuming that the switch uses store-and-forward packet switching, what is the total end-to-end delay to send a packet of length L? (Ignore queuing, propagation delay, and processing delay.)
	- End-to-end delay = L/R1 + L/R2
13. Suppose users share a 2 Mbps link. Also suppose each user transmits continuously at 1 Mbps when transmitting, but each user transmits only 20 percent of the time. (See the discussion of statistical multiplexing in Section 1.3.)
	a. When circuit switching is used, how many users can be supported? **2**
	    
	b. For the remainder of this problem, suppose packet switching is used. Why will there be essentially no queuing delay before the link if two or fewer users transmit at the same time? Why will there be a queuing delay if three users transmit at the same time?  Each user transmits continuously at 1 Mbps when transmitting, so max still = 2. If three users transmit at once then
	![[Pasted image 20250123193501.png]]
	Since the arriving bits is faster than the amount of bits spit out the user will experience queing delay
	    
	c. Find the probability that a given user is transmitting. .2

	d. Suppose now there are three users. Find the probability that at any given time, all three users are transmitting simultaneously. Find the fraction of time during which the queue grows. **Binomial Distribtion**
	18. How long does it take a packet of length 1,000 bytes to propagate over a link of distance 2,500 km, propagation speed 2.5 * 10^8 m/s, and transmission rate 2 Mbps? More generally, how long does it take a packet of length L to propagate over a link of distance d, propagation speed s, and transmission rate R bps? Does this delay depend on packet length? Does this delay depend on transmission rate? 
		0.004 sec + 1 * 10^14
	R19.  The path from HostA to Host B has three links of rates R1 = 500 kbps, R2 = 2 Mbps, and R3 = 1 Mbps.
    1. Assuming no other traffic in the network, what is the throughput for the file transfer? 500 kbps **bottleneck**
    R21. SKIP
	R23.  What are the five layers in the Internet protocol stack? What are the principal responsibilities of each of these layers?
		Application, Transport, Internet, Link, Physical
	R25.  Which layers in the Internet protocol stack does a router process? Which layers does a link-layer switch process? Which layers does a host process?
		Network; Link; Application;
	 
2, 4, 6, 8, 9-13, 20, 21, 24, 25-28

2 Equation 1.1 gives a formula for the end-to-end delay of sending one packet of length L over N links of transmission rate R. Generalize this formula for sending P such packets back-to-back over the N link 

b. Suppose that all connections are between switches A and C. What is the maximum number of simultaneous connections that can be in progress? `
Express the propagation delay, dprop, in terms of m and s. (d/s) (distance/speed) (distance / speed(propgation))
 (bits/bits/sec) (L(in bits) / speed(tranmission))
4 
6
8
9 
10
11
12
13
20
21
24
25
26
27
28
*20 Users supported*
![[Pasted image 20250123170112.png]]
![[Pasted image 20250123170106.png]]

9. 1Mbps = 1,000,000 bits per second