# 📌 Networking Concepts

## OSI MODEL

The `OSI (Open Systems Interconnection) model` is a conceptual model developed by the International Organization for Standardization (ISO) that describes how communications should occur in a computer network. In other words, the OSI model defines a framework for computer network communications. Although this model is theoretical, it is vital to learn and understand as it helps grasp networking concepts on a deeper level. The OSI model is composed of seven layers:

1. Physical Layer
2. Data Link Layer
3. Network Layer
4. Transport Layer
5. Session Layer
6. Presentation Layer
7. Application Layer

#### 1. Physical Layer

The physical layer, also referred to as layer 1, deals with the physical connection between devices; this includes the medium, such as a wire, and the definition of the binary digits 0 and 1.

In simple analogy: This is like the actual water pipe that carries the water from one point to another.

#### 2. Data Link Layer

represents the protocol that enables data transfer between nodes on the same network segment. The data link layer describes an agreement between the different systems on the same network segment on how to communicate. A network segment refers to a group of networked devices using a shared medium or channel for information transfer. For example, consider a company office with ten computers connected to a network switch; that’s a network segment.

In simple analogy: This checks whether what was sent through the pipe arrived correctly and without loss. It checks if the expected amount of water arrived and if there were no leaks during transfer.

#### Layer 3: Network Layer

The data link layer focuses on sending data between two nodes on the same network segment. The network layer, i.e., layer 3, is concerned with sending data between different networks. In more technical terms, the network layer handles logical addressing and routing, i.e., finding a path to transfer the network packets between the diverse networks.

In the data link layer, we gave an example of one company office with ten computers, where the data link layer is responsible for providing a connection between them. Let’s say that this company has multiple offices distributed across various cities, countries, or even continents. The network layer is responsible for connecting the different offices together.

#### Layer 4: Transport Layer

The transport layer, enables end-to-end communication between running applications on different hosts. Your web browser is connected to the TryHackMe web server over the transport layer, which can support various functions like flow control, segmentation, and error correction.

Examples of layer 4 are Transmission Control Protocol (TCP) and User Datagram Protocol (UDP).

#### Layer 5: Session Layer

The session layer is responsible for establishing, maintaining, and synchronising communication between applications running on different hosts. Establishing a session means initiating communication between applications and negotiating the necessary parameters for the session. Data synchronisation ensures that data is transmitted in the correct order and provides mechanisms for recovery in case of transmission failures.

Examples of the session layer are Network File System (NFS) and Remote Procedure Call (RPC).

- Manages the communication between two devices.

#### Layer 6: Presentation Layer

The presentation layer ensures the data is delivered in a form the application layer can understand. Layer 6 handles data encoding, compression, and encryption. An example of encoding is character encoding, such as ASCII or Unicode.

Various standards are used at the presentation layer. Consider the scenario where we want to send an image via email. First, we use JPEG, GIF, and PNG to save our images; furthermore, although hidden from the user by the email client, we use MIME (Multipurpose Internet Mail Extensions) to attach the file to our email. MIME encodes a binary file using 7-bit ASCII characters.

#### Layer 7: Application Layer

The application layer provides network services directly to end-user applications. Your web browser would use the HTTP protocol to request a file, submit a form, or upload a file.

The application layer is the top layer, and you might have encountered many of its protocols as you use different applications. Examples of Layer 7 protocols are HTTP, FTP, DNS, POP3, SMTP, and IMAP. Don’t worry if you are not familiar with all of them.

| Layer Number | Layer Name         | Main Function                                     | Example Protocols and Standards           |
| --- | --- | --- | --- |
| Layer 7      | Application layer  | Providing services and interfaces to applications | HTTP, FTP, DNS, POP3, SMTP, IMAP          |
| Layer 6      | Presentation layer | Data encoding, encryption, and compression        | Unicode, MIME, JPEG, PNG, MPEG            | Layer 5 | Session layer | Establishing, maintaining, and synchronising sessions | NFS, RPC |     | Layer 4 | Transport layer | End-to-end communication and data segmentation | UDP, TCP |
| Layer 3      | Network layer      | Logical addressing and routing between networks   | IP, ICMP, IPSec                           |
| Layer 2      | Data link layer    | Reliable data transfer between adjacent nodes     | Ethernet (802.3), WiFi (802.11)           |
| Layer 1      | Physical layer     | Physical data transmission media                  | Electrical, optical, and wireless signals |

## IP Addresses and Subnets

Given:
192.168.10.70/26

1. Find the subnet mask
   IPv4 address has 32bits that are divided into 4octets, each octets has 8bits
   8bits . 8bits . 8bits . 8bits
   8 + 8 + 8 + 8 = 32
   32 - 26 = 6

1 = network
0 = hosts

/26 is the network (1)
6 is the host (0)

11111111.11111111.11111111.11000000

| 1   | 1   | 1   | 1   | 1   | 1   | 1   | 1   | .   | 1   | 1   | 1   | 1   | 1   | 1   | 1   | 1   | .   | 1   | 1   | 1   | 1   | 1   | 1   | 1   | 1   | .   |  1  | 1   | 0   | 0   | 0   | 0   | 0   | 0   |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | :-: | --- | --- | --- | --- | --- | --- | --- |
| -   | -   | -   | -   | -   | -   | -   | -   | -   | -   | -   | -   | -   | -   | -   | -   | -   | -   | -   | -   | -   | -   | -   | -   | -   | -   | -   | 128 | 64  | 32  | 16  | 8   | 4   | 2   | 1   |

128 + 64 = 192

SUBNET MASK:
`255.255.255.192`

2. Find the block size
   Formula:
   256 - last octet of the subnet mask
   last octet: `192`
   so:
   256 - 192 = 64

BLOCK SIZE
`64`

3. Make a range
   start from 0 then add 64 then make it a range:
   0-63
   64-127
   128-191
   192-255

4. Find on the range where the ip belongs:
   IP: 70
   It belongs to: 64-127

5. Find the network and the broadcast
   if the ip is in 64-127
   then:
   the first number is the network address: 64 (192.168.10.64)
   the last number is the broadcast address: 127 (192.168.10.127)

6. Host Range
   Usable host is in between:
   65 - 126
   so:
   192.168.10.65 - 192.168.10.126

7. Number of Hosts:
For /26
Host Bits:
32 - 26 - 6
Host
2^6 - 2 = 62


For 192.168.10.70/26
|Item|Answer|
|-|-|
|Subnet Mask|255.255.255.192|
|Network Address|192.168.10.64|
|Broadcast Address|192.168.10.127|
|Usable Host Range| 192.168.10.65 - 192.168.10.126|
|Number of Hosts| 62|

## Telnet

The `TELNET (Teletype Network) protocol` is a network protocol for remote terminal connection. In simpler words, telnet, a TELNET client, allows you to connect to and communicate with a remote system and issue text commands.
