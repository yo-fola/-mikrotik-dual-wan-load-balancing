Lessons Learned

Overview

This project gave me practical experience configuring a MikroTik RB951Ui for a dual-WAN network.

The work covered LAN bridging, DHCP, WAN configuration, NAT, independent ISP testing, and the planned use of PCC load balancing and automatic failover.

The main lessons from the project were not only about individual RouterOS settings, but also about how different networking components depend on one another.

⸻

1. WAN and LAN Interfaces Must Be Clearly Separated

One of the first lessons was understanding the role of each physical interface.

The router was designed as:

ether1 = ISP 1 / WAN 1
ether2 = ISP 2 / WAN 2
ether3 = LAN
ether4 = LAN
ether5 = LAN

The WAN interfaces were kept separate from the LAN bridge.

This helped me understand that WAN and LAN ports may exist on the same router, but they have different networking roles.

⸻

2. A Bridge Can Turn Multiple Physical Ports Into One Logical LAN

I created:

bridge-LAN

and added:

ether3
ether4
ether5

This allowed all three interfaces to operate as part of the same internal network.

Conceptually:

ether3 ──┐
ether4 ──┼── bridge-LAN
ether5 ──┘

This helped me better understand the relationship between physical interfaces and logical network structures.

⸻

3. The Router Needs an Address on the LAN

I assigned:

192.168.10.1/24

to:

bridge-LAN

This made the MikroTik the gateway for devices on:

192.168.10.0/24

This reinforced the role of the default gateway in allowing client devices to communicate beyond their local network.

⸻

4. DHCP Simplifies Client Configuration

I configured a DHCP server on:

bridge-LAN

so connected devices could automatically receive addresses in the:

192.168.10.x

range.

This made it unnecessary to configure every LAN device manually.

It also provided a simple test of whether the LAN was working correctly.

If the connected laptop received a valid address from the expected subnet, it confirmed that the LAN bridge and DHCP configuration were functioning.

⸻

5. Each WAN Should Be Tested Independently

One of the most important lessons from the project was to test each ISP separately before adding more advanced routing.

The process was:

Configure ISP 1
      ↓
Test ISP 1 alone
      ↓
Configure ISP 2
      ↓
Test ISP 2 alone
      ↓
Proceed to load balancing

This makes troubleshooting easier.

If PCC is configured before verifying that both WAN links work independently, it becomes harder to determine whether a problem is caused by:

* The ISP
* DHCP
* NAT
* Routing
* PCC

Testing the basic connection first reduces unnecessary complexity.

⸻

6. A DHCP Client Must Successfully Bind

Both WAN interfaces were configured as DHCP clients.

The expected status was:

bound

This showed that the MikroTik had successfully received network configuration from the upstream ISP.

This gave me a practical way to verify whether the WAN interface had been configured successfully before troubleshooting higher-level issues.

⸻

7. NAT Is Required for LAN Devices to Access the Internet

The LAN uses private addresses from:

192.168.10.0/24

I configured masquerade rules for both WAN interfaces:

ether1 → masquerade
ether2 → masquerade

This helped me understand the role of source NAT in allowing internal devices using private addresses to access external networks.

The traffic flow became:

LAN Device
     |
     v
 MikroTik
     |
     v
    NAT
     |
     v
WAN Interface
     |
     v
 Internet

⸻

8. Two ISPs Do Not Automatically Become One Faster Connection

An important concept I learned was that connecting two ISPs does not automatically combine them into one faster connection.

For example:

ISP 1 = 5 Mbps
ISP 2 = 5 Mbps

does not normally mean:

One download = 10 Mbps

Instead, the intended PCC design distributes different connections across the available WAN links.

Example:

Connection 1 → ISP 1
Connection 2 → ISP 2
Connection 3 → ISP 1
Connection 4 → ISP 2

This means the network can make use of approximately 10 Mbps of aggregate capacity across multiple connections, while an individual connection generally remains on one WAN path.

⸻

9. PCC Works at the Connection Level

The planned load-balancing method for this project is:

PCC — Per Connection Classifier

The key idea is that different connections can be assigned to different WAN links.

This is different from simply alternating individual packets between both ISPs.

The intended behavior is:

Connection A → ISP 1
Connection B → ISP 2
Connection C → ISP 1
Connection D → ISP 2

This helped me understand why connection classification is important in multi-WAN routing.

⸻

10. A Second ISP Alone Does Not Guarantee Failover

Another important lesson was that simply connecting a second ISP does not automatically create redundancy.

Failover requires the router to detect when one WAN is no longer usable and route traffic through the remaining connection.

The intended behavior is:

Both WANs available
        ↓
PCC load balancing

If WAN 1 fails:

WAN 1 ✕
WAN 2 ✓
        ↓
Use WAN 2

If WAN 2 fails:

WAN 1 ✓
WAN 2 ✕
        ↓
Use WAN 1

The actual failover rules and health checks are still to be completed and documented.

⸻

11. Troubleshooting Should Be Done in Layers

This project reinforced the value of checking the simpler parts of the network first.

A useful troubleshooting order is:

Physical Connection
        ↓
LAN Bridge
        ↓
DHCP
        ↓
WAN DHCP
        ↓
NAT
        ↓
Internet Connectivity
        ↓
PCC
        ↓
Routing
        ↓
Failover

Instead of changing several RouterOS settings at once, each stage should be verified before moving to the next.

This makes it easier to identify where the actual problem exists.

⸻

12. Connectivity Should Be Verified Before Complexity Is Added

A major lesson from the project was:

Get the basic network working first.
Then add the advanced routing.

The LAN was verified before WAN configuration.

Each WAN was tested before PCC.

NAT was configured before advanced load balancing.

This approach reduces the number of possible failure points during troubleshooting.

⸻

Skills Developed

Through this project, I gained practical exposure to:

* MikroTik RouterOS
* WinBox
* Ethernet interface management
* LAN bridging
* IPv4 addressing
* DHCP
* NAT
* Masquerading
* Dual-WAN design
* PCC concepts
* WAN failover concepts
* Network testing
* Structured troubleshooting

⸻

Current Project Progress

Completed

LAN Bridge
LAN Addressing
DHCP
WAN 1 Configuration
WAN 2 Configuration
NAT for WAN 1
NAT for WAN 2
Independent ISP Testing

Still to Complete

PCC Rules
Connection Marking
Routing Configuration
WAN Health Checking
Automatic Failover
Failover Testing
Final Evidence
Sanitized RouterOS Export

⸻

Final Lesson

The most important lesson from this project is that a multi-WAN network is not created simply by connecting two internet connections to a router.

Each part of the design has a specific role:

LAN Bridge
    +
DHCP
    +
WAN Connectivity
    +
NAT
    +
PCC
    +
Failover
    =
Resilient Multi-WAN Network

The project also showed me that testing each layer independently makes network troubleshooting more accurate and easier to manage.