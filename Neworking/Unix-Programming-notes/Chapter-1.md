to write programs (clients and servers) that communicate across a network, one must first invent a protocol and high level decisions about which program is expected to initiate communication and when responses are expected, must be made.

The server must be able to handle multiple clients at the same time
Even though the client and server speak to each other via the application protocol, they *transmit* using lower level layers
![[Pasted image 20250129162721.png]]