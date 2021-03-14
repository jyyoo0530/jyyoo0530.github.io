---
layout: post
title:  "Network Glossary"
---

### Glossary

- **Connection**: Refers to pieces of related information that are transferred through the internet before the data transfers, and then deconstructed at the end of the data transfer.
- **Packet**: Most basic unit that is transferred over a network. Packets are the envelopes that carry your data in pieces from one end point to the other.
- **Network Interface**: Any kind of software interface to networking hardware. If you have network card in your computer, network interface is the contorlling software controls network card.
- **LAN**: Stands for "Local Area Network". A portion of ( or a ) network that is not publicly accessible to the greater internet.(Home or Office network)
- **WAN**: Stands for "Wide Area Network". Comparing with LAN, WAN describe larger networks that connects to the internet.
- **Protocol**: A set of rules and standards that basically define a language that devices can use to communicate. Multiple protocols are being used in different network layers.
- **Port**: An address on a single machine(software) that can be tied to a specific piece of software. This is not a physical interface or location, but allows the server to be abel to communicate with multiple applications.
- **Firewall**: A program that decide whether allows in/outbound traffic from your server. Generally, firewall controls(blocks) port but not a specific application.
- **NAT**: Stands for Network Address Translation.It is a way to translate requests that are incoming into a routing server to the relevant devices or servers that it knows about in the LAN. This is usually implemented in physical LANs as a way to route requests through one IP address to the necessary backend servers.
- **VPN**: Stands for Virtual Private Network. Connecting seperate LANs through internet, while maintaining privacy. Being used in accessing remote system.

### Network Layers

#### OSI Model (Open Systems Interconnection Reference Model)
Used to be known as network 7-layers.
<img src="/resources/osi.gif" width="750" height="700"><br>
- **Application**: The layer that users and user-end-application most often interact with.
- **Presentation**: The presentation layer is responsible for mapping resources and creating context. Being used to translate lower level networking data into data that applications expect to see.
- **Session**: The layer that handles connection layer. Create/Maintain/Destroys connection between nodes in a persistent way.
- **Transport**: The transport layer is responsible for handing the layers above it a reliable connection. In this context, reliable refers to the ability to verify that a piece of data was received intact at the other end of the connection.
- **Network**: Route data bewtween different nodes on the network. Addresses which computer to send information to. Also can break apart larger message into smaller chunk which can be reassembled at the destination.
- **Data Link**: With using physical connection, establish and maintain reliable links.
- **Physical**: Actual devices that used to make a connection.(like Ethernet)

####TCP/IP Model
Simpler and widely being used.
- **Application**: In this model, the application layer is responsible for creating and transmitting user data between applications. The applications can be on remote systems, and should appear to operate as if locally to the end user.
- **Transport**: The transport layer is responsible for communication between processes. This level of networking utilizes ports to address different services. It can build up unreliable or reliable connections depending on the type of protocol used.
- **Internet**: The internet layer is used to transport data from node to node in a network. This layer is aware of the endpoints of the connections, but does not worry about the actual connection needed to get from one place to another. IP addresses are defined in this layer as a way of reaching remote systems in an addressable manner.
- **Link**: The link layer implements the actual topology of the local network that allows the internet layer to present an addressable interface. It establishes connections between neighboring nodes to send data.

Reference: https://www.digitalocean.com/community/tutorials/an-introduction-to-networking-terminology-interfaces-and-protocols
