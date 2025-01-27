# Chapter 2 Application Layer
In this chapter, we study the conceptual and implementation aspects of network applications.
- Web, 
- e-mail
- DNS
- peer-to-peer (P2P) file distribution
- video streaming.

*Overall this will give you a solid foundation of how our core application protocols work.* Having deep knowledge about these protocols help you make more informed decisions for both enterprise software and systems so this should be fun - P1erce.
**We also provide several fun and interesting socket programming assignments at the end of the chapter.**

## 2.1 Principles of Network Applications
Applications are on the network edge  **not the core**
### 2.1.1 Network Application Architectures
The **network architecture** is fixed and provides a specific set of services to applications. 

The **application architecture** on the other hand, is designed by the application developer and dictates how the application is structured over the various end systems.

There are two types of **application architecture**
- **Client-Server** : always-on host (server), which has a **static IP Address**,  which services requests from many other hosts, called **clients**. (Web, Ftp, telnet, e-email)
	- For larger scale apps (like Google) there are thousands of servers which are housed in a **data ceneter** that cluster into one large virtual server
- **peer to peer** :   application exploits direct communication between pairs of intermittently connected hosts, called **peers** (e.g BitTorrent).
	- **Self Scalable** : although each peer generates workload by requesting files, each peer also adds service capacity to the system by distributing files to other peers
	- don’t require significant server infrastructure and bandwidth (cost-effective)
	- Faces challenges of security, performance, and reliability due to its decentralized nature

### **2.1.2 Processes Communicating**
When processes can communicate either on the same host (signals, pipes (message passing), memory sharing) but two processes **running on different hosts** can communicate **messages** **bi-directionally** via each end-system's **socket** (which instead of pipe, which are one way (think about `cat hello.txt | grep hello` as one way communication (which is also on the 1 system as opposed to sockets which handle message passing with 2 different systems)) ) 
#### Client and Server Processes
While we have two different **application architectures** we say the system providing some service (website, file, etc.) is the **server** and the host requesting the data as the **client** (even in **P2P**)
*in P2P file sharing, a process can be both a client and a server. Indeed, a process in a P2P file-sharing system can both upload and download files. Nevertheless, in the context of any given communication session between a pair of processes, we can still label one process as the client and the other process as the server.*

#### The Interface Between the Process and the Computer Network
**Again** A process sends messages into, and receives messages from, the network through a software interface called a **socket**.
**In addition** the socket the interface between the application layer and the transport layer within a host, thus it's also called the **Application Programming Interface (API)**  application and the network. 
- *I like to think about APIs in the context of syscalls. The OS has core functionalities (creating process (fork), killing process (exit), printing data (write), etc.). To **expose** these core OS functionalities to the programmer, the OS provides these programmable **syscalls**. *
*The programmer has very little control beyond the back half of the socket (transport layer and below), except for the selection of transport-layer protocol (TCP/UDP) and some transport-layer parameters such as maximum buffer and maximum segment sizes*

![[Pasted image 20250124214253.png]]

#### **Addressing Processes**
Each host has its own IP 
Each socket has its own port number
### 2.1.3 Transport Services Available to Applications
The choice of TCP or UDP depends on your application type
#### Reliable Data Transfer
If a protocol provides a **guaranteed data delivery service it's said to provide reliable data transfer**
This reliable data transfer functionality of a transport protocol is unnecessary for **loss tolerant apps**

#### Throughput

**Throughput** is the rate bits are sent from process A to process B. This rate may be negatively impacted by the other sessions using the wire. 

TCP can provide a guaranteed available throughput at some specified rate.

This guarantee of available throughput is important for **bandwidth-sensitive applications**, apps that have specific throughput requirements (multi-media apps) and the transport protocol may be able to adapt its behavior to meet these requirements.

**elastic applications** (Electronic mail, file transfer, and Web transfers) can make use of as much, or as little, throughput as happens to be available.

#### Timing
A transport-layer protocol can also provide **timing guarantees** (which come in many forms) (e.g may be that every bit that the sender pumps into the socket, it arrives at the receiver’s socket no more than 100 msec later).

![[Pasted image 20250125150318.png]]
#### Security
a transport protocol can provide an application with one or more security services such as **encryption** providing **confidentiality to both hosts**
A transport protocol can also provide other security services such as **data integrity** and **end-point authentication**

## 2.1.4 Transport Services Provided by the Internet
#### TCP Services
- Connection oriented : establishes a connection via a **3-way-handshake** before sending data
- Reliable data transfer : acknowledgement of the data received and data is repeated in there is no replied acknowledgement from receiver to sender 
*Not mentioned it book but worth mentioning if ur ever asked about it*
- Sequencing (Forward Acknowledgement) : To ensure segments are re-assembled in the proper order, the client and server exchange the expected **next** seq number (Host a -- [Seq 10] -> Host b)
									(Host a <- [Ack 11] -- Host b) 
- Flow Control : Destination host can tell the source host to increase/decrease the rate that data is sent based on the **window size** (which is a field in the TCP header that allows more data to be sent before an acknowledgment is required)
#### UDP Services
*None of the TCP services and no additional services*
![[Pasted image 20250125151657.png]]

#### 2.1.5 Application-Layer Protocols
While yes that network processes communicate with each other by sending messages into sockets, we need to know how these messages as structured (they're formatted differently depending on the protocol), and this format is dictated by the **Application Protcol** (HTTP, SMTP, FTP, DNS, etc.)
- Types of messages sent (Req, res, etc.)
- Available fields how they're delineated from each other 
- The meaning (*semantics*)  of the information in each field
- **How** and **when** a process sends and receives/responds to messages 
For example HTTP determines the format and sequence of messages exchanges between a client and a Web server. Websites such as **Netflix** leverage multiple protocols depending the service request (plain website or video hosted on the website)

That all said An **Application-Layer Protocol** is only one part of an entire application (e.g Netflix)

### 2.1.6 Network Applications Covered in This Book
- The Web
-  electronic mail
-  directory service (DNS)
-  video streaming
-  and P2P applications

## 2.2 The Web and HTTP
It was a big deal mainly because of it's on-demand convenience of information and the development of the WWW

### 2.2.1 Overview of HTTP
Hyper-Text-Transfer-Protocol (HTTP) is the Web’s application-layer protocol and is implemented in two programs: a client program and a server program

These two programs exchange **HTTP messages** and **HTTP** defines teh structure of these message and how the client and server exchange these messages.

**Terminology**
- **Web page** : collection of objects
- **Object** : A file (HTML, CSS, JS, image, video clip)
- **Base HTML File** : Main page that references other files
	- if a Web page contains HTML text and five JPEG images, then the Web page has six objects
- **URL**. : Reference to another object in the page and is comprised of
	-  hostname of the website (www.someSchool.edu)
	- object path (/someDepartment/picture. gif)
		- www.someSchool.edu/someDepartment/picture.gif
- **Browser** : HTTP Client
- **Web Server** : HTTP Server
**Properties of HTTP**
**HTTP is Stateless** :  Server sends requested files to clients without storing any state information about the client. If a particular client asks for the same object twice in a period of a few seconds, the server does not respond by saying that it just served the object to the client; instead, the server resends the object, as it has completely forgotten what it did earlier.

**Application Architecture** : **Client-server**

**HTTP** uses a request-response cycle ![[Pasted image 20250125153614.png]]
**HTTP** need not worry about lost data or the details of how TCP recovers from loss or reordering of data within the network.

### 2.2.2 Non-Persistent and Persistent Connections
The client and server send various req/res messages and so we need to decide if each request/response pair be sent over a **separate (non-persistent)** TCP connection, or should all of the requests and their corresponding responses be sent over the **same (persistent)** TCP connection?

*The book investigates the advantages and disadvantages of both below as **HTTP can use both** even though the **default is persistent**, but HTTP clients and servers can be configured to use non-persistent connections*
#### HTTP with Non-Persistent Connections
- The HTTP client process initiates a TCP connection via sockets which are also used in the data transfer
- HTTP client sends an HTTP request message to the server via its socket (e.g GET www.hostname.com/index.html)
-  HTTP Server receives request message through its socket, retrieves the object, encapsulates the object in an HTTP response message, and sends the response message to the client via its socket.
- The HTTP server process signals to the socket TCP to close the TCP connection, but TCP will ensure the message is recived
- HTTP client receives the response message. The TCP connection terminates.
	- `close()` system call is used to gracefully shutdown the socket via the kernel (the kernel handles all of this for you. So in the kernel file (C file (tcp_input.c)) that handles this web connection, part of that program's exit handling will make this close() syscall )
- First 4 steps are repeated for any objects referenced in HTML  (images, etc.)

*The steps above illustrate the use of non-persistent connections, where each TCP connection is closed after the server sends the object*

Each **non-persistent** TCP connection transports exactly one request message and one response message. Thus, in this example, when a user requests the Web page (1 HTML file that references 10 images), 11 TCP connections are generated.

Browsers may open multiple TCP connections and request different parts of the Web page over the multiple connections to shorten overall response time.

In short : *a brand-new connection must be established and maintained for each requested object in non-persistent thus:*
- TCP buffers must be allocated and TCP variables must be kept in both the client and server.
- each object suffers a delivery delay of two RTTs—one RTT to establish the TCP connection and one RTT to request and receive an object.

**Round Trip Time w/ HTTP Overview**

**RTT Model for HTTP request for file**
![[Pasted image 20250125163157.png]]

**Round-Trip Time** : the time it takes for a small packet to travel from client to server and then back to the client. (hence the name *round-trip*, which includes all possible delays)

*TODO: Ask about this cause the book doesn't make this sense*

*I think since the HTTP object is part of the response message the total RTT should be the following:* 

*I say*
**HTTP RTT = (TCP RTT + HTTP REQ/RES RTT)**

*The book says*
**HTTP RTT = (TCP RTT + HTTP REQ RTT + the transmission time at the server of the HTML file)**

*Further explanation of the RTT*

**TCP RTT** = The first **two parts** of the three-way handshake

For browser to initiate a TCP connection between the browser and the Web server a TCP three-way handshake occurs (note these **SYN & ACK** FLAGS are actually flag **bits** set in the **TCP L4 Header shown below**)

**TCP Header**
![[Pasted image 20250125163825.png]]
**TCP Three-way Handshake to establish connection between two TCP sockets**
![[Pasted image 20250125163532.png]]
The first two parts of the three-way handshake take one RTT. After completing the first two parts of the handshake, the client sends the HTTP request message combined with the third part of the three-way handshake (the acknowledgment) into the TCP connection.

**HTTP REQ RTT** : The client sends the HTTP request message combined with the third part of the three-way handshake

#### HTTP with Persistent Connections
Does HTTP piplining work with one page if there are multiple hosts?

Persistent connections, the server leaves the TCP connection open after sending a response so all objects can use the same TCP connection and if there are requests for multiple pages on **one** server, these requests can be made without waiting for replies (pipelining)

### 2.2.3 HTTP Message Format
There are two types of HTTP messages, request messages and response messages
**request line (method (GET, POST, HEAD, PUT, and DELETE), URL, HTTP Version**
GET /somedir/page.html HTTP/1.1 : 
**header lines**
Host: www.someschool.edu | Specifies the host on which the object resides. (required by Web proxy cache)
Connection: close    | Client tells server to close the connection after sending the requested object
User-agent: Mozilla/5.0  | The browser type that is making the request to the server (send different versions of the same object to different types of user agents)
Accept-language: fr | receive a French version of the object, if such an object exists on the server

![[Pasted image 20250125170216.png]]

![[Pasted image 20250125165756.png | 500 x 300]]

**Request Lines**
**GET :** Request object identified in the URL field
**POST** : Request a Web page from the server, but the specific contents of the Web page depend on what the user entered into the form fields
**HEAD** : Responds with an HTTP message but it leaves out the requested object
**PUT**: upload an object to a specific path
**DELETE** : delete an object on a Web server

#### HTTP Response Message
It has three sections: 
- initial status line,   Protocol version, a status code, status message
	**HTTP/1.1 200 OK**  
- six header lines 
	**Connection: close**   tell the client that it is going to close the TCP connection after sending the message
	**Date: Tue, 18 Aug 2015 15:44:04 GMT**  The time and date when the **HTTP response** was created and sent by the server
	**Server: Apache/2.2.3 (CentOS)**   Analogous to the `User-agent`
	**Last-Modified: Tue, 18 Aug 2015 15:11:03 GMT**  the time and date when the object was created or last modified (critical for object caching)
	**Content-Length: 6821**  number of **bytes** in the object being sent
	**Content-Type: text/html**  indicates that the object in the entity body is HTML text (determined by MAGIC bytes not file extension)
- entity body.
(data data data data data ...)
**Status Codes Overview**
- 200 OK: Request succeeded and the information is returned in the response.
- 301 Moved Permanently: Requested object has been permanently moved; the new URL is specified in Location: header of the response message. The client software will automatically retrieve the new URL.
- 400 Bad Request: This is a generic error code indicating that the request could not be understood by the server.
- 404 Not Found: The requested document does not exist on this server.
- 505 HTTP Version Not Supported: The requested HTTP protocol version is not supported by the server.
![[Pasted image 20250125171624.png]]
![[Pasted image 20250125171650.png]]

### 2.2.4 User-Server Interaction: Cookies
It is often desirable for a Web site to identify users, but HTTP stateless to maintain its performance (so it doesn't have to keep track of sessions). To identify users HTTP uses cookies

**Cookie Overview**
cookie technology has four components
- a cookie header line in the HTTP response message
- a cookie header line in the HTTP request message
- a cookie file kept on the user’s end system and managed by the user’s browser
- a back-end database
![[Pasted image 20250125173736.png]]

the request comes to Web server, the server creates a unique identification number and creates an entry in its back-end database which is included in the HTTP response via the  `Set-cookie` header

The browser then adds a line containing the server hostname and the Set-cookie: header ID number to the special cookie file. The browser will consult this **cookie file** for each subsequent request to this server, and user data is delinated by this Set-cookie ID

*using a combination of cookies and user-supplied account information, a Web site can learn a lot about a user and potentially sell this information to a third party.*

### 2.2.5 Web Caching

A **Web cache (proxy server)** is a network entity that satisfies HTTP requests on the behalf of an origin Web server. It's both a *client* and a *server* at the same time

A users browser can be configured so all requests first directed to the Web cache [RFC 7234]

1. The browser establishes a TCP connection to the Web cache and sends an HTTP request for the object to the Web cache.
2. The Web cache checks to see if it has a copy of the object stored locally. If it does, the Web cache returns the object within an HTTP response message to the client browser.
3. If the Web cache does not have the object, the Web cache opens a cache-to-server TCP connection and makes the HTTP request for the object and receives the HTTP response
4. Web cache receives the object, stores a copy in its local storage and sends a copy to the client browser (over the existing TCP connection between the client browser and the Web cache)
*Advantages of cache*
- Reduce the response time for a client request
- Reduce traffic on an institution’s access link to the Internet
- can substantially reduce Web traffic in the Internet as a whole
This overall reduces the **traffic intensity** on lower speed links that would alternatively bottleneck the performance of the web app

#### The Conditional GET

The copy of an object residing in the cache may be stale (inconsistent with the current object state on the web server)
HTTP has a mechanism that allows a cache to verify that its objects are up to date via **conditional GETs** [RFC 7232]


When the cache receives an object from the **origin server** it stores **the last-modified** date (along with the object) and uses it in its 

**If-modified-since:** `Header` of **conditional GET**
![[Pasted image 20250125180747.png]]

*If the object has **NOT** been modified*
![[Pasted image 20250125180816.png]]


*If the object **HAS** been modified*
*response include the body data*

### 2.2.6 HTTP/2
HTTP/2 [RFC 7540]
The primary goals for HTTP/2 are:
1. Reduce perceived latency by enabling request and response multiplexing over a single TCP connection
2. Provide request prioritization and server push, 
3. Provide efficient compression of HTTP header fields.

**HTTP/2 DOESN'T CHANGE**
HTTP methods, status codes and URLs, and header fields 

**HTTP/2 DOES CHANGE**
How the data is formatted and transported between the client and server

*Why HTTP2 Came about (yes it's important)*
Remember HTTP is a **persistent** protocol by default, so only one TCP connection per Web page. This introduces a **Head of Line (HOL) blocking problem.**  In a series of requests of a video clip, HTML files, images, the **heavier data at head of the line** (the video clip) blocks the small objects behind it.

**HTTPv1.1 Solution**
Open multiple parallel TCP connections, thereby having objects in the same web page sent in parallel to the browser. *HTTP/2 seeks to get rid of (or at least reduce the number of parallel connections) and dodge HOL blocking*

[[Chapter-2_My_Questions]]

#### HTTP/2 Framing
The HTTP/2 solution for HOL blocking is to break each message into small frames and interleave the request and response messages on the same TCP connection. The ability to break down an HTTP message into independent frames, interleave them, and then reassemble them on the other end is the single most important enhancement of HTTP/2.

The HTTP/2 framing sub-layer handles the framing. Whenever an HTTP Request **or** Response is sent, the sublayer breaks up the header into a frame and body section of its body into one or more frames, which are concurrently sent with other HTTP messages (either other request or response messages). The sublayer also **binary-encodes**  all frames, which is more efficient.

#### Response Message Prioritization and Server Pushing
**Message prioritization** allows developers to allow their browsers to assign a weight between 1 and 256 (highest priority) to each message and dictate the priority of the message and its dependencies (using other messages' weights). 

**Server Pushing** An HTML object may reference other dependencies (images, stylesheets, etc.). Instead of waiting for the client to *explicitly* make these requests, the server can **push** these responses to the client as it knows by the HTML contents what the client *will* need. This eliminates the extra latency due to waiting for the requests.

#### HTTP/3
a new “transport” protocol that is implemented in the application layer over the bare-bones UDP protocol. QUIC has several features that are desirable for HTTP, such as message multiplexing (interleaving), per-stream flow control, and low-latency connection establishment, but it has yet to be standardized. 
## 2.3 Electronic Mail in the Internet
Internet mail has three major components: 
- user agents (allow users to read, reply to, forward, save, and compose messages) (Outlook, Gmail)
- mail servers Intermediate storage of emails for both sender and reciever
	- Each user has a **mailbox** in one of the mail servers
	- If the sender server cannot deliver mail to the receiver’s server, the sender server holds the message in a **message queue** and attempts to transfer the message later, or delete it if can't after several days of trying (usually every 30 minutes) 
- Simple Mail Transfer Protocol (SMTP) The application-layer protocol for Internet electronic mail.
	- Uses TCP
	- Client-server (but runs on every mail server as each mail server, depending on the connection, can be either a client or a server)
![[Pasted image 20250125192856.png]]
### 2.3.1 SMTP
SMTP, defined in [RFC 5321]
Restricts the body (not just the headers) of all mail messages to simple 7-bit ASCII, which requires binary multimedia data to be encoded to ASCII before being sent over SMTP, and requires the corresponding ASCII message to be decoded back to binary after SMTP transport (not a thing in HTTP)

1. Alice instructs the user agent to send the email message specifying  bob@someschool.edu).
2. Alice’s user agent sends the message to her mail server, where it is placed in a **message queue**.
3. The client side of SMTP, running on Alice’s mail server, sees the message in the message queue. It opens a TCP connection to an SMTP server, running on Bob’s mail server.
4. After some initial SMTP handshaking, the SMTP client sends Alice’s message into the TCP connection.
5. At Bob’s mail server, the server side of SMTP receives the message. Bob’s mail server then places the message in Bob’s mailbox.
6. Bob invokes his user agent to read the message at his convenience.
*Note*
SMTP does not normally use intermediate mail servers for sending mail, even if the receiver's mail server is down, that message will remain on the sender's mail server

**How SMTP Sends Messages**
*TCP connection established a connection to port 25 at the **sending server** SMTP to **receieving server***
*Additional Application Handshake*
*Message Sent*
*Close Connection*
![[Pasted image 20250125192354.png]]

### 2.3.2 Mail Message Formats
This peripheral information is contained in a series of header lines separated by blank lines are defined in RFC 5322.

each header line contains readable text, consisting of a keyword followed by a colon followed by a value.

Every header must have a From: header line and a To: header line; all others are optional

*These are different from the SMTP commands even if they contain the same info such as from and to*
![[Pasted image 20250125192747.png]]

#### 2.3.3 Mail Access Protocols
Localhosts don't have SMTP enabled on their local hosts, instead the mail server is shared with users who access that mail server over the internet (or on their local network)

By having Alice first deposit the e-mail in her own mail server, Alice’s mail server can repeatedly try to send the message to Bob’s mail server, say every 30 minutes, until Bob’s mail server becomes operational.

Since SMTP is only a push operation, the user agent will use **HTTP** or mainly **IMAP (defined in RFC 3501) or POP3** to retrieve Bob’s e-mail.

## 2.4 DNS—The Internet’s Directory Service
one identifier for a host is its hostname, but this isn't the network layer identifier for hosts (IPs are).
### 2.4.1 Services Provided by DNS
UDP and uses port 53
In order to reconcile these preferences between machines and humans we need a **directory service** that **translates hostnames to IP addresses** (DNS)

The Internet's Domain Name System (DNS) defined in RFC 1034 and RFC 1035
- distributed database implemented in a hierarchy of DNS servers
- an application-layer protocol that allows hosts to query the distributed database
1. The same user machine runs the client side of the DNS application.
2. The browser extracts the hostname, www.someschool.edu, from the URL and passes the hostname to the client side of the DNS application.
3. The DNS client sends a query containing the hostname to a DNS server.
4. The DNS client eventually receives a reply, which includes the IP address for the hostname.
5. Once the browser receives the IP address from DNS, it can initiate a TCP connection to the HTTP server process located at port 80 at that IP address.
*Note there is added delay because the browser needs to query the DNS record associated with the hostname, though the desired IP address is often cached in a "nearby" DNS server*
**DNS Additional Features**
- **Host aliasing** : Shortened versions of the original canonical hostname ()
- Mail server aliasing : Same as above, but just applied to mail servers
- Load distribution : Busy sites, such as cnn.com, are replicated over multiple servers, and DNS can distribute the load between these servers even if they have the same hostname  (which can have a set of IPs associated with it)


### 2.4.2 Overview of How DNS Works
gethostbyname() function call that an app calls to make a DNS request in the users host and a DNS reply message will provide the desired mapping, which is then passed to the invoking application

#### A Distributed, Hierarchical Database
A centralized design (where one server exists) is inappropriate for today's internet 

No single DNS server has all of the mappings for all of the hosts in the Internet.

There are the types of DNS server (organized in a hierarchy)
- root DNS servers : provide the IP addresses of the TLD servers (.com,.org,.net,.edu,.gov)
- top-level domain DNS servers provide the IP addresses for authoritative DNS servers (hostnames)
- authoritative DNS server  houses DNS records for the individual server (mail, web, etc.)
Also (not part of the hierarchy) but when a host makes a DNS query, the query is sent to the local DNS server (**recursive query**), which acts a proxy, forwarding the query into the DNS server hierarchy (**iterative queries**), 

#### DNS Caching
when a DNS server receives a DNS reply  it can cache the mapping in its local memory
### 2.4.3 DNS Records and Messages
The actual mappings stored in each DNS sever is called a resource records (RRs) which is a four-tuple that contains the following fields: (Name, Value, Type, TTL)

**(Limited) Record Types**

- type=A Name is a hostname and Value is the IP address (Authoritative name server only)
- type=NS Name is a domain (such as foo.com) and Value is the hostname of an authoritative DNS server that knows how to obtain the IP addresses for hosts in the domain.
-  type=CNAME Name is the alias hostname and Value is a canonical hostname
- type=MX - hostname Name Value is the canonical name of a mail server
#### DNS Messages
Three are DNS query and reply messages
![[Pasted image 20250125195947.png]]

#### Inserting Records into the DNS Database
Skipped for brevity, but you really just buy the domain

## 2.5 Peer-to-Peer File Distribution
Now we're using P2P not Client-server
![[Pasted image 20250125200522.png]]
#### Scalability of P2P Architectures
The distribution time is the time it takes to get a copy of the file to all N peers
- The server must transmit one copy of the file to each of the N peers. Thus, the server must transmit NF bits. Since the server’s upload rate is us, the time to dis- tribute the file must be at least NF/us.
- The host with the lowest obtain all F bits of the file in less than F/dmin seconds. Thus, the minimum distribution time is at least F/dm
- ![[Pasted image 20250125200345.png]]

## 2.6 Video Streaming and Content Distribution Networks
We will see streaming video are implemented using application-level protocols and servers that function in some ways like a cache.
## 2.6.1 Internet Video
A video is a sequence of images, typically being displayed at a constant rate, for example, at 24 or 30 images per second.

An uncompressed, digitally encoded image consists of an array of pixels, with each pixel encoded into a number of bits to represent luminance and color, though we have modern compression algorithms, which allow varying quality but also demands on the network.

Given video's heavy data usage (bps) the most important performance measure for streaming video is average end- to-end throughput, so as to provide continuous playout, the network must provide an average throughput to the streaming app (at least as large as the bit rate of the compressed video).
### 2.6.2 HTTP Streaming and DASH
**HTTP GET** is sent for a video file stored on the server, and once bytes received into the client application buffer, the client application begins playing back by grabbing video frames, decompressing the frames, and displaying them on the screen.Thus, the video streaming application is displaying video as it is receiving and buffering frames corresponding to latter parts of the vide

*The previously mentioned method a major shortcoming*
- All clients receive the same encoding of the video, despite the available bandwidth
This has lead to the inception of **Dynamic Adaptive Streaming over HTTP (DASH)**
The video is **encoded** into several different versions, with each version having a different bit rate and, correspondingly, a different quality level.
- With DASH, each video version is stored in the HTTP server, each with a different URL
- HTTP server also has a **manifest file**  a URL for each version along with its bit rate, which the client first requests
- The client also measures the received bandwidth and will choose a chunk (of video bytes) from a high-bitrate version (the opposite is true) 

## 2.6.3 Content Distribution Networks

A centralized data center housing all of say YTs videos is not gonna work, 1.) end-to-end bandwidth bottlenecks 2.) Redudant videos are sent (which also costs the company money cause it's going through its provider ISP) 3. Single-point-of-failure

To meet the challenge of distributing massive amounts of video data all major video-streaming companies make use of **Content Distribution Networks (CDNs)**.

A CDN is a network of servers (geographically dispersed) storing videos, and attempts to direct each user request to a node in the CDN that provides the best user experience there are both **private CDNs** owned by the content provider (Google) and **third-party CDN** on behalf of multiple content providers

*Where do we place the servers?* CDNs typically adopt one of two different server placement philosophies
- **Enter Deep** deploy server clusters in **access ISPs** all over the world (the most local tier of ISPs).
- **Bring Home**   bring the ISPs home to a central location and the users come to it

Server's don't each get their own **entire** repository of videos, instead they use a pull strategy; if a client requests a video from a cluster that the cluster doesn't have, the cluster will ask another if it has it and stores a copy locally while streaming the video
#### CDN Operation
It's literally the same as anything else on the internet
1. click link to make HTTP GET
2. Initial recursive query and subsequent iterative query to find the IP of the CDN node
3. TCP connection established, manifest file is sent, and start receiving data
#### Cluster Selection Strategies
**cluster selection strategy** How a client gets directed to the proper CDN node
- **geographically closest**
	- Con : This is not based on network hops
	- Con : Varies depending on Local DNS Server
	- Con : ignores the variation in delay and available band- width over time
- **real-time measurements**  Measures of loss performance (via ping probes) between their clusters and clients (which are the Local DNS Servers)
	- Local DNS Servers may not respond to such ping probes 
### 2.6.4 Case Studies: Netflix and YouTube
*This is how I get excited about this kinda stuff, like yeah all above stuff is OK, but learning how these major media apps are designed, built, and function, is Awesome!*

*Go read it if you're interested!* (I'll post notes later)
##  2.7 Socket Programming: Creating Network Applications (In Python)
*Skipped for brevity* *Since all applications are coded in C*