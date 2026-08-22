PCC Load Balancing

Overview

This section documents the planned implementation of Per Connection Classifier (PCC) on the MikroTik RB951Ui.

The purpose of PCC is to distribute separate network connections between the two available WAN links.

The dual-WAN environment uses:

WAN 1 → ether1 → ISP 1
WAN 2 → ether2 → ISP 2

At this stage of the project, both WAN links had already been configured and tested independently.

⸻

Why PCC Is Needed

Connecting two ISPs to a router does not automatically combine their bandwidth.

Without additional routing logic, the router still needs a method for deciding which WAN interface should carry each connection.

PCC provides a way to classify connections and distribute them across multiple WAN links.

Conceptually:

Connection 1 → ISP 1
Connection 2 → ISP 2
Connection 3 → ISP 1
Connection 4 → ISP 2

This allows both internet connections to be used across multiple simultaneous sessions.

⸻

Example Capacity

For example:

ISP 1 = 5 Mbps
ISP 2 = 5 Mbps

The network may have approximately:

10 Mbps aggregate capacity

across multiple concurrent connections.

This does not normally mean that one individual TCP connection or one single download will operate at 10 Mbps.

A single connection will generally follow one WAN path.

The benefit becomes more visible when several devices or several applications generate multiple simultaneous connections.

⸻

Conceptual Traffic Distribution

Without PCC:

LAN
 |
 v
MikroTik
 |
 v
One selected WAN

With PCC:

                    MikroTik
                       |
                PCC Classification
                  /           \
                 /             \
                v               v
             WAN 1            WAN 2
             ISP 1            ISP 2

The router classifies different connections and assigns them to different WAN paths.

⸻

Example Connection Flow

A simplified example:

Client A - Web Connection 1 → ISP 1
Client A - Web Connection 2 → ISP 2
Client B - Application       → ISP 1
Client C - Streaming Session → ISP 2

The classification happens at the connection level rather than simply alternating every individual packet.

This is important because packets belonging to the same connection should normally continue to follow the same WAN path.

⸻

Planned PCC Components

The final PCC implementation is expected to require configuration involving:

* Connection classification
* Mangle rules
* Connection marks
* Routing marks or routing tables
* Separate WAN routes
* Appropriate NAT handling
* Failover-aware routing

These components will be documented after the final RouterOS configuration is verified.

⸻

Why Connection Consistency Matters

A connection should not normally have its packets randomly switched between different public-facing WAN paths.

For example:

Packet 1 → ISP 1
Packet 2 → ISP 2
Packet 3 → ISP 1

would not represent the intended PCC behavior for a single connection.

Instead, the expected concept is:

Connection A → ISP 1
Connection B → ISP 2
Connection C → ISP 1
Connection D → ISP 2

This preserves consistency for each classified connection.

⸻

Prerequisites Completed Before PCC

Before starting PCC, the following were verified:

* bridge-LAN configured
* LAN DHCP working
* WAN 1 configured
* WAN 2 configured
* NAT configured for both WAN interfaces
* ISP 1 tested independently
* ISP 2 tested independently

This reduces the number of possible causes if a problem appears during PCC configuration.

⸻

Troubleshooting Approach

PCC should only be introduced after basic connectivity is confirmed.

The troubleshooting sequence is:

LAN working
    ↓
WAN 1 working independently
    ↓
WAN 2 working independently
    ↓
NAT working
    ↓
Configure connection classification
    ↓
Verify connection marks
    ↓
Verify routing decisions
    ↓
Test both WAN paths

If load balancing fails, the configuration can then be investigated without questioning whether the basic WAN links are functional.

⸻

Validation Goals

Once the final PCC configuration is completed, validation should confirm that:

* New connections are distributed between WAN 1 and WAN 2.
* Connections assigned to WAN 1 exit through ISP 1.
* Connections assigned to WAN 2 exit through ISP 2.
* LAN clients continue to have internet access.
* NAT works correctly on both WAN paths.
* Existing connections remain stable.
* The configuration works alongside failover.

⸻

Evidence to Add

The final evidence should include screenshots of:

evidence/
├── 08-pcc-mangle-rules.png
├── 09-connection-marks.png
├── 10-routing-tables.png
└── 11-pcc-validation.png

Where possible, sensitive public addresses and credentials should be removed or obscured before publication.

⸻

Current Status

Completed

* Dual WAN physical configuration
* WAN DHCP clients
* NAT for both WAN interfaces
* Independent WAN testing

Pending

* Final PCC classifier rules
* Mangle rules
* Connection marks
* Routing marks / routing tables
* PCC validation screenshots

⸻

Expected Result

The intended result is:

                   INTERNET
                   /      \
               ISP 1      ISP 2
                  |          |
               WAN 1      WAN 2
                  \          /
                   \        /
                     PCC
                      |
                   MikroTik
                      |
                  bridge-LAN
                      |
                    Clients

PCC will allow the router to use both WAN links across multiple connections instead of leaving one connection unused during normal operation.