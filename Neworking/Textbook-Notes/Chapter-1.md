# Chapter 1 Computer Networks and the Internet
## 1.1 What Is the Internet?
### 1.1.1 A Nuts-and-Bolts Description
All devices connected to the internet are called **hosts** or **end-systems**
There are different communication links that connect these hosts including **fiber**, **copper**, **coax**, and **radio** spectrum all of which transmit at different transmission rates (measured in bits/second)

End systems access the internet via (low tier) ISPs and there are different types of ISPs such as Home (Copper, Cable (TV) Modem, and DSL) University, Corporate WiFi ISPs and Cellular ISPs
- These lower tier ISPs connect to higher-tier ISPs with higher speed routers connected by fiber and the upper-tier ISPs are connected to each other 
- the computers and other devices connected to the Internet are often referred to as end systems (including servers, laptops, etc.). They are referred to as end systems because they sit at the edge of the Internet. 
In order for devices to be able to communicate with each other, a standardized protocol must be used across all different types of systems 
These standards are outlined by the **IETF** as **RFCs** however for internet standards the **IEEE** specifies **Ethernet** and **WiFi** standards think **802.3** Ethernet and **802.11** WiFi

### 1.1.2 A Services Description
Instead of looking at the internet in a 'technical' description with routers and end-systems, we can look at the internet as the infrastructure that provides services to applications. Think about email or text, or even web surfing. These apps needs an infrastructure for data to get from point A --> point B. These apps also include mobile apps.
- The applications that run on end systems are said to be **Distributed** Applications because they involve multiple **end-systems** exchanging data with each other. Think about a video confrencing app with 10 people, all those **end-systems** of those 10 users are exchanging VoIP  data with each other. 
- These apps run on end systems NOT the network core (routers & switches)
- Although (routers & switches) facilitate the exchange of data they're **NOT** concerned with the application data whatsoever (unless it's a NGFW which does application-layer inspection)

How does one program running on one end system instruct the Internet to deliver data to another program running on another end system? **A Socket** as well as defining **How** one program should send the data

### 1.1.3 What Is a Protocol?
What is a protocol? What does a protocol do?
*A protocol is a rule set for how host A should send **some type** of data (HTTP, TCP, STP) to host B.*  So all a protocol does is define **what messages** should be sent between two hosts using **A** protocol and how each host should respond to those messages. Take the TCP protocol and its three-way handshake to establish a connection SYN -> SYN ACK -> ACK

**Book Definition:** A protocol defines the format and the order of messages exchanged between two or more communicating entities, as well as the actions taken on the transmission and/or receipt of a message or other event.

## 1.2 The Network Edge
This section focuses on the components at the **edge** of the internet namely, the computers, smartphones, etc.

Throughout this book we will use the terms hosts and end systems interchangeably; that is, host = end system. They're called hosts because they **host** some application such as a browser. 
### 1.2.1 Access Networks
the access network is the network that connects to an edge router which connects to the internet

For Broadband connection to the internet for residential access the two most common types are digital subscriber line (DSL)(Telephone) and cable (TV).
#### Home Access: DSL, Cable, FTTH, and 5G Fixed Wireless
**DSL**
-- DSL Modem -> CO -- INSIDE CO -> DSLAM ---> Internet
- each customer’s DSL modem uses the existing telephone line exchange data with the **Digital-Subscriber-Line-Access-Multiplexer**(DSLAM) located in the telco's **Central Office** (CO)
- Takes the internet digital data and converts it into high-frequency tones (which are sent across the telephone line) and converts it back into internet digital data by the teleco company's DSLAM that then connects to the internet. *Yes Hundreds or even thousands of households connect to a single DSLAM.*
- ![[Pasted image 20250121211023.png]]
- The residential telephone line carries both data and traditional telephone signals simultaneously, which are encoded at different frequencies. This approach makes the single DSL link appear as if there were **three separate links**. this is related to FDM and TDM.
	_•_ _A high-speed downstream channel, in the 50 kHz to 1 MHz band_
	_•_ _A medium-speed upstream channel, in the 4 kHz to 50 kHz band_
	_•_ _An ordinary two-way telephone channel, in the 0 to 4 kHz band_
- **DSLAM Transmission Rates**
	- Because the downstream and upstream transmission rates are different DSL acces is said to be **asymmetric**
	- Transmission rates are negatively impacted by distance and electrical interference
	- downstream transmission rates 24 Mbs and 52 Mbs, 
	- upstream rates of 3.5 Mbps and 16 Mbps; 
	- the newest standard provides for aggregate upstream plus downstream rates of 1 Gbps [ITU 2014] 

**Cable**
Uses TV cable lines. Home has **Cable Modem** which converts the digital internet data to analog signals, and the CMTS inside the ISPs Cable Head End does the reverse before sending it to the internet.

House -- COAX Connection (Analog Signals) --> -- Fiber nodes ->  --(FIBER Connection)--> Cable Head End --(Inside cable end) --> Cable Modem Termination System 
You can think of the **Cable Head End** the same as the teleco's Central Office and the Cable Modem Termination System (CMTS) as the DSLAM

*Because this network utilizes both Fiber **AND** Coax* this home connection is referred to as Hybrid Fiber Coax (HFC)
![[Pasted image 20250121212114.png]]

- **Data Over Cable Service Interface Specification (DOCSIS) Transmission Rates**
	- Also Asymmetric
	- **DOCSIS 2.0** 40 Mbps (down) 30 Mbps (up)
	- **DOCSIS 3.0** 1.2 Gbps (down) and 100Mbps (up)
	- As in the case of DSL networks, the maximum achievable rate may not be realized due to lower contracted data rates _or media impairments.
	- travels downstream on every link to every home and every packet sent by a home travels on the upstream channel to the head end
**fiber to the home (FTTH)**  This provides fiber from the CO directly to the home

**Optical Distribution Network Types**
- Direct Fiber (Connection between CO and splitter is shared, but between Homes and the splitter, individual fiber lines are linked to each home)
- **Optical Distribution Network Architecture Types** (Two types of network architctures that perform this splitting)
	- Active Optical Networks (AONs) --> switched Ethernet 
	- Passive Optical Networks (PONs) --> 
		- Each home has an connects their 'edge router' to their Optical Network Terminator 
		- Homes individual fiber lines are connected to one line via a Optical Splitter
		- The Single fiber line between the splitter and CO is terminated by the **Optical Line Terminator**
		- all packets sent from OLT to the splitter are replicated at the splitter
		- PON Distribution Architecture ![[Pasted image 20250122042334.png]]
**5G Wireless**
Beam-forming technology (radio spectrum) and data is sent wirelessly from the provider’s base station to the a modem in the home. A WiFi wireless router is connected to the modem 

#### Access in the Enterprise (and the Home): Ethernet and WiFi
- Ethernet -> Wired connection via switches
- WiFi -> Data sent to/from Wireless Access Point (which connects to the internet via Wired ethernet)
	- ![[Pasted image 20250122043210.png]]
#### Wide-Area Wireless Access: 3G and LTE 4G and 5G
These devices employ the same wireless infrastructure used for cellular telephony to send/receive packets through a base station that is operated by the cellular network provider. 

### 1.2.2 Physical Media
Data is sent through a series of sender a receiver pairs connected by some physical medium. These **DO NOT** have to be the same between pairs only within (think about HFC)

**Types of Physical Media**
- Guided Media --> Waves are **guided** along a **physical** medium (fiber, copper, coax)
- Unguided Media --> Waves propagate in the atmosphere and in outer space (WiFi, Satellite)
Interestingly it's NOT the medium, but rather the **Work needed for Installation**  that costs the most
#### Twisted-Pair Copper Wire
- *Most common transmission media*
- Exactly as it sounds *pairs* of of copper wire are twisted to reduce Electro Magnetic Interference (EMI)
#### Coaxial Cable
- two copper conductors but the two conductors are concentric rather than parallel
- with this construction and special insulation and shielding, coaxial cable can achieve high data transmission rates.
- the modem shifts the digital signal to a specific frequency band, and the resulting analog signal is sent from the transmitter to one or more receivers.
- Shared medium where multiple end systems can directly connect to the cable
#### Fiber Optics
- Uses pulses of light, with each pulse representing a bitA
- tens or even hundreds of gigabits per second.
- Immune to EMI
- Very low signal attenuation up to 100 kilometer Low 
- **Optical Carrier (OC-_n_) Transmission Rates**
	 - **link speed equals n * 51.8 Mbps**
	 - OC-1
	- OC-3
	- OC-12
	- OC-24
	- OC-48
	- OC-96
	- OC-192
	- OC-768
#### Terrestrial Radio Channels
- Radio channels carry signals in the electromagnetic spectrum
- They require no physical wire to be installed, can penetrate walls
**Environmental considerations** 
- path loss and shadow fading (which decrease the signal strength as the signal travels over a distance and around/through obstructing objects)
- multipath fading (due to signal reflection off of interfering objects)
- interference (due to other transmissions and electromagnetic signals)
**Terrestrial Radio Channels Types**
- Short range (1-2m) (med devices & bluetooth, NFC)
- Medium range (10-[1,2,3,4]00m) (WiFi)
- Long range spanning tens of kilometers (cellular)
#### Satellite Radio Channels
-The satellite receives transmissions on one frequency band, regenerates the signal using a repeater and transmits the signal on another frequency. 
- substantial signal propagation delay (280 ms)
- only used if DSL and cable are not an option
**Two Types of Satellites used**
- geostationary satellites --> permanently remain above the same spot on Earth
- low-earth orbiting (LEO) satellites  --> Many satellites orbiting earch
	- Not used for internet connectivity
## 1.3 The Network Core
The core of the internet is made up of packet switches (**routers**)

**FROM NOW ON PACKET SWITCHES = ROUTERS** (This is just my personal preference)
![[Pasted image 20250122050947.png]]

### 1.3.1 Packet Switching

App data is broken into "**packets**" and travel across a physical medium from one *Packet Switch*(L3 switch or Router) to another
Packets are transmitted over each communication link at a rate equal to the full transmission rate of the link (L (# of bits) * R (bits/sec) = L/R seconds)

#### Store-and-Forward Transmission
Store-and-Forward Transmission means that the ENTIRE packet must be received BEFORE the first bit of that packet can be sent (bits of packet stored in buffer)

![[Pasted image 20250122051611.png]]
*Given L/R* seconds between each device the following happens

| Time | nthPacket on Router | nthPacket on Des               |
| ---- | ------------------- | ------------------------------ |
| L/R  | Packet 1            | N/A                            |
| 2L/R | Packet 2            | Packet 1                       |
| 3L/R | Packet 3            | Packet 1 & Packet 2            |
| 4L/R | Packet 4            | Packet 1 & Packet 2 & Packet 3 |

End to End Delay
![[Pasted image 20250122052233.png]]

#### Queuing Delays and Packet Loss
for each link connected to the router has an **output queue** (buffer) which stores the packets
If the link is busy (some other packets are in front) the packet has to wait **queuing delay** (baed on the congestion level of the network)
- If the buffer for the queue (output buffer) is full and another packet arrives **packet loss** occurs
#### Forwarding Tables and Routing Protocols
*Skipped for brevity* but the idea is that for a routers to send the packet to the next destination it uses a "**routing table**" which answers the following 
*To send the packet to destination X send it to next hop (next host) Y out of interface Z*
There are also different types of **routing protocols (RIP, EIGRP, OSPF)** which govern *how* the router learns routes to destination networks and we'll (hopefully) go very in-depth on this topic
#### 1.3.2 Circuit Switching
In a circuit switched network, the links and buffers are *reserved* for the duration of the communication session *this is NOT the case in packet switched networks (hence the queue)*
Traditional telephone networks are examples of circuit-switched networks.
- the switches on the path between the sender and receiver maintain connection state for that connection. This is known as a **circuit**
- When circuit is established it reserves (guarantees) a constant transmission rate 
- number of links = number of simultaneous connections
- Transmission Capacity of One Link = Capacity / Nlinks
	- If each link between adjacent switches has a transmission rate of 1 Mbps, then each end-to-end circuit-switch connection gets 250 kb
- must first reserve one circuit on each of two links.
	- Notice the first link is used then the second![[Pasted image 20250122053504.png]]
#### Multiplexing in Circuit-Switched Networks
 **Circuit-Switched Implementation Types** 
 multiplexing is specifically a solution for sharing one physical medium among multiple users/connections via establishing circuits. We can separate out these circuits on one physical medium in two ways
 - frequency-division multiplexing (FDM) the link dedicates a frequency band to each connection. Think about radio they share a specific frequency spectrum, similarly each the link dedicates a frequency band to each connection within its own frequency spectrum (in the above case there would be 4)
 - time-division multiplexing (TDM) Each circuit gets it's own time slice within a physical link
*Weaknesses of Circuit Switching*
- circuit switching can be wasteful because the dedicated circuits are idle during **silent periods**  (nobody's talking, so nobody is using the link, but it still can't be used to transmit other data (unlike packet switching))
- establishing end-to-end circuits and reserving end-to-end transmission capacity is complicated and requires complex sign- aling software to coordinate the operation of the switches along the end-to-end path.
#### Packet Switching Versus Circuit Switching
Packet switching is not suitable for real-time services (phone calls, video conference calls) b/c of its end-to-end delays
*Counter Argument*
- it offers better sharing of transmission capacity than circuit switching 
- it is simpler, more efficient, and less costly to implement than circuit switching.

*Why is packet switching more efficient?*
It's hard for me the wrap my head around this but here is the breakdown (partly from AI but I've inspected and validated its correctness )
**Example 1**
-  Setup:
	- Total link capacity: 1 Mbps
    - Each active user needs: 100 kbps
    - Users are only active 10% of the time
    - When active, they generate data at a constant rate
- Circuit Switching Analysis:
    - Each user gets a dedicated slice of bandwidth (100 kbps) whether they use it or not
    - Maximum number of users = 1 Mbps ÷ 100 kbps = 10 users
    - This is inefficient because 90% of the time, each user's allocated bandwidth is wasted
- Packet Switching Analysis:
	- Bandwidth is allocated dynamically based on who's actually sending data
		- When 10 or fewer users are active (99.96% of time):
		- Each active user can get up to 100 kbps or more depending on how many others are active
			- If only 5 users are active, they could each get 200 kbps
	- Each user has 0.1 (10%) probability of being active at any time
	- The system can support 35 users with very little risk of congestion
	- Why? Because probability of having 11+ active users (which would exceed capacity) is only 0.0004 (0.04%)
	- This means 99.96% of the time, the system works as well as circuit switching

Because the probability of having more than X simultaneously active users is minuscule in this example, packet switching provides essentially the same performance as circuit switching, but does so while allowing for more than three times the number of users.

**Example 2 (Pasted from book)**
Let’s now consider a second simple example. Suppose there are 10 users and that one user suddenly generates one thousand 1,000-bit packets, while other users remain quiescent and do not generate packets. Under TDM circuit switching with 10 slots per frame and each slot consisting of 1,000 bits, the active user can only use its one time slot per frame to transmit data, while the remaining nine time slots in each frame remain idle. It will be 10 seconds before all of the active user’s one million bits of data has been transmitted. In the case of packet switching, the active user can continuously send its packets at the full link rate of 1 Mbps, since there are no other users generating packets that need to be multiplexed with the active user’s packets.

Circuit switching pre-allocates use of the transmission link regardless of demand, with allocated but unneeded link time going unused. Packet switching on the other hand allocates link use on demand.

### 1.3.3 A Network of Networks
Yes Homes, Companies etc, connect to ISPs  but ISPs themselves must connect with each other

- One naive approach would be to have each access ISP directly connect with every other access ISP.
- Network Structure 1, interconnects all of the access ISPs with a single global transit ISP
	- Since the access ISP pays the global transit ISP, the access ISP is said to be a **customer** and the global transit ISP is said to be a **provider**.
- Network Structure 2, consists of the hundreds of thousands of access ISPs and multiple global transit ISPs (two tier)
	- Smaller ISPs can now choose among the competing global transit providers as a function of their pricing and services
	- The global ISPs must connect with each other otherwise access ISPs connected to one of the global transit providers would not be able to communicate with access ISPs connected to the other global transit providers.
Network Structure 2 (preferred) has a caveat, no global ISP has presence in each and every city in the world to connect to the access ISP thus there is an intermediary **regional ISP**. (three-tier network) this becomes *Network Structure 3*
- **Network Structure 3**
	- regional ISP to which the access ISPs in the region connect
	- Each regional (access) ISP connects to the their **Tier-1** (global) ISP 
Network Structure 3 also contains Points of Presence (PoPs)
- group of one or more routers (at the same location) in the provider’s network where customer ISPs can connect into the provider ISP
Network Structure 3 also has ISPs who **multi-home**
- Any ISP (except for tier-1 ISPs) connects to two or more provider ISPs.
	- A access ISP may multi-home with two regional ISPs
	- A regional ISP may multi-home with multiple tier-1 ISPs.
	- When an ISP multi-homes, it can continue to send and receive packets into the Internet even if one of its providers has a failure.	

Network Structure 4
Customer ISPs pay their provider ISPs to obtain global Internet interconnectivity which is based on the amount of data transfer. To dodge this, lower-tier ISPs connect with each other, so that traffic doesn't need to go upstream to the provider ISP (regional or tier-1 (global)), so it doesn't count towards their bill. When two ISPs **peer** with each other
- Along these same lines, a third-party company can create an Internet Exchange Point (IXP), which is a meeting point where multiple ISPs can peer together.

Network Structure 4 = access ISPs, regional ISPs, tier-1 ISPs, PoPs, multi-homing, peering, and IXPs (Not Network Structure 3)

Network Structure 5 (don't worry this is the last one)
builds on top of Network Structure 4 by adding content-provider networks. 
Google has it's own private network that, while does connect to tier-1 ISPs, and pays those ISPs for the traffic it exchanges with them,  reduces its payments to upper-tier ISPs and has greater control of how its services are ultimately delivered to end users, by connect to lower tier ISPs and IXP
**The Internet Core**
Connection between two regional ISPs (not through IXPs) is a **multi-home**

![[Pasted image 20250122063929.png]]

## 1.4 Delay, Loss, and Throughput in Packet-Switched Networks
### 1.4.1 Overview of Delay in Packet-Switched Networks
A packet moving from one destination to another experiences the following delays (together they accumulate the  **total nodal delay**)

**Nodal Processing Delay**  --> Time required to inspect L3 Header and checksums and decide where to send the packet next 

**Queuing Delay** --> The wait for packet to be transmitted onto the link

**Transmission Delay** --> Time required to push (that is, transmit) all of the packet’s bits into the link L(size of data in bits)/R (bits/sec) (tranmission speed)

**Propagation Delay** --> Time required for one bit to travel from one end of the link to the other
dnodal = dproc + dqueue + dtrans + dprop
*Difference between Transmission and Propagation Delay*
The transmission delay is the amount of time required for the router to push out the packet; it is a function of the packet’s length and the transmission rate of the link, but has nothing to do with the distance between the two routers. The propagation delay, on the other hand, is the time it takes a bit to propagate from one router to the next; it is a function of the distance between the two routers, but has nothing to do with the packet’s length or the transmission rate of the link.

- Link speed (transmission rate) = how fast the router can push out bits
- Propagation speed = how fast those bits travel through the cable/fiber/medium once they've left the router
#### 1.4.2 Queuing Delay and Packet Loss
The ratio (a = packets/sec) L (length of packet in bits)a (rate at which packets arrive at the queue)/R (bits/sec), called the traffic intensity,
If La/R > 1, then the average rate at which bits arrive at the queue exceeds the rate at which the bits can be transmitted from the queue. Thus it's imperative to follow this *golden rule* **Design your system so that the traffic intensity is no greater than 1**
![[Pasted image 20250123193509.png]]
#### Packet Loss
Again if the ouput queue (buffer for packets ) is full, the router will start dropping packets

### 1.4.3 End-to-End Delay
![[Pasted image 20250122075748.png]]

#### Traceroute
As these packets work their way toward the destination, they pass through a series of routers. When a router receives one of these special packets, it sends back to the source a short message that contains the name and address of the router
Suppose there are N - 1 routers between the source and the destination. Then the source will send N special packets into the network, with each packet addressed to the ultimate destination.

Traceroute actually repeats the experiment just described three times, so the source actually sends 3 • N packets to the destination.

The output has six columns: the first column is the n value described above, that is, the number of the router along the route; the second column is the name of the router; the third column is the address of the router (of the form xxx.xxx.xxx.xxx); the last three columns are the round-trip delays for three experiments packet losses are marked with an asterisk
![[Pasted image 20250122154500.png]]

There are a number of free software programs that provide a graphical interface to Traceroute; one of our favorites is PingPlotter [PingPlotter 2020].
#### End System, Application, and Other Delays
Application Delay : An end system may purposefully delay its transmission as part of its application protocol 

Packetization Delay : In VoIP, the sending side must first fill a packet with encoded digitized speech before passing the packet to the Internet
#### 1.4.4 Throughput in Computer Networks
If the file consists of F bits and the transfer takes T seconds for Host B to receive all F bits, then the average throughput of the file transfer is F/T bits/sec.
Rs < Rc (bits "flow" ) at a rate of Rs bps and giving a throughput of Rs bp
Rc < Rs, bits will only leave the router at rate Rc,  giving an end- to-end throughput of Rc
Thus, for this simple two-link network, the throughput is min{Rc, Rs} - any additional delay
***end-to-end throughput of a stream is governed by the throughput at the bottleneck link.)***

![[Pasted image 20250122162333.png]]
the constraining factor for throughput in today’s Internet is typically the access network
## 1.5 Protocol Layers and Their Service Models

### 1.5.1 Layered Architecture
#### Protocol Layering
![[Pasted image 20250122163230.png]]

| Layer       | PDU       | Protocol Examples or Medium                                    |
| ----------- | --------- | -------------------------------------------------------------- |
| Application | Message   | HTTP, SMTP, FTP                                                |
| Transport   | Segment   | TCP, UDP                                                       |
| Network     | Datagrams | IP                                                             |
| Link        | Frame     | Ethernet, WiFi, and the cable access network’s DOCSIS protocol |
| Physical    | Bits      | Twisted-pair copper wire, single-mode fiber optics             |
Data is encapsulated with its corresponding layer which is eventually sent over a medium as bits, and is then de-encapsulated
![[Pasted image 20250122163942.png]]
# Bits & Bytes 
*Not part of the textbook but this is something you might need to answer questions*
![[Pasted image 20250123164639.png]]
### 1.5.2 Encapsulation
Skipped for brevity
# 1.6 Networks Under Attack
Skipped for brevity
# 1.7 History of Computer Networking and the Internet
Skipped for brevity

