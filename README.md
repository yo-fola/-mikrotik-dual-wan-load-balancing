MikroTik Dual-WAN Load Balancing & Failover

A hands-on networking project using a MikroTik RB951Ui to build a dual-WAN network with centralized LAN connectivity, DHCP, NAT, independent ISP connections, and a planned PCC load-balancing and automatic failover architecture.

Project Status: LAN configuration, DHCP, both WAN interfaces, NAT, and independent ISP testing have been completed. PCC load balancing and automatic failover will be added and documented as the project progresses.

⸻

Project Objectives

The objective of this project is to configure a MikroTik router to:

* Connect to two independent Internet Service Providers.
* Use both WAN connections efficiently.
* Provide one internal LAN for connected devices.
* Automatically assign IP addresses using DHCP.
* Provide internet access using NAT.
* Distribute connections between both ISPs using PCC.
* Maintain internet connectivity if either ISP becomes unavailable.

⸻

Hardware and Environment

Component	Configuration
Router	MikroTik RB951Ui
Management Interface	WinBox
WAN 1	ether1
WAN 2	ether2
LAN Ports	ether3, ether4, ether5
LAN Bridge	bridge-LAN
Router LAN IP	192.168.10.1/24
LAN Network	192.168.10.0/24
Client Addressing	DHCP

⸻

Network Architecture

                 INTERNET
                 /      \
             ISP 1      ISP 2
                |          |
             ether1      ether2
                 \        /
                  \      /
             MikroTik RB951Ui
                    |
                bridge-LAN
               /     |     \
          ether3  ether4  ether5
             |       |       |
          Laptop    PCs    Network

Interface Assignment

ether1 = ISP 1 / WAN 1
ether2 = ISP 2 / WAN 2
ether3 = LAN
ether4 = LAN
ether5 = LAN

ether1 and ether2 are kept outside the LAN bridge because they are used as WAN interfaces.

⸻

Implementation

1. Connected to the MikroTik

I connected my laptop to one of the LAN interfaces and used WinBox to access the MikroTik router.

The router was initially accessed using its MAC address from the WinBox Neighbors section.

⸻

2. Created the LAN Bridge

I created a bridge named:

bridge-LAN

The following interfaces were added to the bridge:

ether3
ether4
ether5

This grouped the three physical LAN interfaces into one logical internal network.

ether3 ──┐
ether4 ──┼── bridge-LAN
ether5 ──┘

⸻

3. Configured the LAN Gateway

I assigned the following IP address to bridge-LAN:

192.168.10.1/24

This created the following LAN:

Network: 192.168.10.0/24
Gateway: 192.168.10.1

Connected devices use the MikroTik router as their default gateway.

⸻

4. Configured DHCP

I configured a DHCP server on:

bridge-LAN

The DHCP server automatically provides IP addresses to devices connected to the internal network.

Example client addresses:

192.168.10.10
192.168.10.11
192.168.10.12

I confirmed that my laptop received an address in the:

192.168.10.x

range.

This verified that the LAN bridge and DHCP configuration were working correctly.

⸻

5. Configured ISP 1

ISP 1 was connected to:

ether1

I configured ether1 as a DHCP client with:

Interface: ether1
Add Default Route: Yes
Use Peer DNS: Yes

I verified that the DHCP client displayed:

Status: bound

This confirmed that ISP 1 successfully provided the MikroTik with network configuration.

⸻

6. Configured NAT for ISP 1

I created a source NAT rule:

Chain: srcnat
Out Interface: ether1
Action: masquerade

This allows devices using private addresses on the LAN to access the internet through ISP 1.

LAN Device
192.168.10.x
     |
     v
 MikroTik
     |
    NAT
     |
     v
  ether1
     |
   ISP 1
     |
 Internet

⸻

7. Tested ISP 1

Before introducing the second ISP, I tested ISP 1 independently.

From the MikroTik terminal:

ping 8.8.8.8

I also tested internet connectivity from the connected laptop.

Testing one ISP at a time makes troubleshooting easier before introducing multi-WAN routing.

⸻

8. Configured ISP 2

ISP 2 was connected to:

ether2

I configured ether2 as another DHCP client:

Interface: ether2
Add Default Route: Yes
Use Peer DNS: Yes

I verified that the DHCP client also reached:

Status: bound

At this stage the MikroTik had two separate WAN connections.

⸻

9. Configured NAT for ISP 2

A second source NAT rule was created:

Chain: srcnat
Out Interface: ether2
Action: masquerade

Both WAN interfaces now had their own NAT rules.

⸻

10. Tested Both ISPs Independently

Before configuring PCC, I tested each WAN connection separately.

ISP 1 Test

ISP 2 was disconnected.

LAN
 |
MikroTik
 |
ISP 1
 |
Internet

ISP 2 Test

ISP 1 was disconnected.

LAN
 |
MikroTik
 |
ISP 2
 |
Internet

Both connections should work independently before advanced routing is introduced.

⸻

PCC Load Balancing

The next stage of this project is to implement Per Connection Classifier (PCC).

PCC allows MikroTik RouterOS to classify network connections and distribute them between multiple WAN interfaces.

Conceptually:

Connection 1 → ISP 1
Connection 2 → ISP 2
Connection 3 → ISP 1
Connection 4 → ISP 2

For example, if:

ISP 1 = 5 Mbps
ISP 2 = 5 Mbps

the network can provide approximately:

10 Mbps aggregate capacity

across multiple simultaneous connections.

This does not normally mean that one single download will use the combined 10 Mbps.

PCC distributes separate connections across the available WAN links.

⸻

Automatic Failover

The final network is intended to provide WAN redundancy.

Normal Operation

ISP 1 ✓      ISP 2 ✓
     \        /
      \      /
        PCC
         |
        LAN

ISP 1 Failure

ISP 1 ✕
ISP 2 ✓
   |
MikroTik
   |
  LAN

ISP 2 Failure

ISP 1 ✓
   |
MikroTik
   |
  LAN
ISP 2 ✕

The router should automatically use the remaining functional connection when one WAN becomes unavailable.

⸻

Troubleshooting Methodology

One of the main lessons from this project is the importance of testing each network layer separately.

My troubleshooting approach is:

Physical connection
        ↓
WAN DHCP
        ↓
Gateway
        ↓
Routing
        ↓
NAT
        ↓
DNS
        ↓
Internet connectivity
        ↓
PCC classification
        ↓
Routing decisions
        ↓
Failover

Instead of changing multiple RouterOS settings at once, each part of the network should be verified independently.

⸻

Current Project Status

Completed

* Connected to MikroTik using WinBox
* Assigned WAN and LAN interfaces
* Created bridge-LAN
* Added ether3, ether4, and ether5 to the bridge
* Assigned 192.168.10.1/24 to the LAN
* Configured DHCP
* Configured WAN 1
* Configured WAN 2
* Configured NAT for WAN 1
* Configured NAT for WAN 2
* Tested both ISPs independently

Next Stages

* Configure PCC classifier rules
* Configure connection marking
* Configure routing marks / routing tables
* Configure WAN health checking
* Configure automatic failover
* Test WAN 1 failure
* Test WAN 2 failure
* Validate PCC traffic distribution
* Add screenshots and evidence
* Add sanitized RouterOS configuration export

⸻

Evidence

Configuration screenshots and testing evidence will be added to the evidence/ directory.

Planned evidence includes:

evidence/
├── 01-interface-layout.png
├── 02-lan-bridge.png
├── 03-lan-ip.png
├── 04-dhcp-server.png
├── 05-wan1-dhcp.png
├── 06-wan2-dhcp.png
├── 07-nat-rules.png
├── 08-pcc-rules.png
├── 09-routing.png
└── 10-failover-test.png

Sensitive information such as passwords, authentication credentials, ISP secrets, administrative credentials, and private keys will not be published.

⸻

Technologies and Concepts

* MikroTik RouterOS
* MikroTik RB951Ui
* WinBox
* TCP/IP
* Ethernet
* DHCP
* NAT
* Masquerading
* Network bridging
* Routing
* Multi-WAN networking
* Per Connection Classifier
* WAN failover
* Network troubleshooting

⸻

What I Learned

This project gave me practical experience configuring and troubleshooting network infrastructure.

Key lessons include:

* Connecting two ISPs does not automatically combine their bandwidth.
* Multi-WAN networks require routing logic to determine how connections use each WAN.
* NAT must be configured correctly for outbound traffic.
* LAN bridging allows several physical interfaces to operate as one logical network.
* Each WAN should work independently before load balancing is introduced.
* Redundancy requires both an alternative link and routing logic that can use it.
* Troubleshooting is more effective when network layers are tested individually.

⸻

Final Goal

Dual ISP Connectivity
        +
PCC Load Balancing
        +
Automatic Failover
        +
NAT
        +
DHCP
        =
Resilient Multi-WAN Network