WAN Configuration

Overview

This section documents the configuration of the two WAN interfaces on the MikroTik RB951Ui.

The router was designed to use:

ether1 = ISP 1 / WAN 1
ether2 = ISP 2 / WAN 2

Each WAN interface was configured as a DHCP client so that the MikroTik could automatically receive addressing and gateway information from each upstream ISP.

Before configuring PCC load balancing, both internet connections were tested independently.

⸻

1. WAN Interface Assignment

The WAN interfaces were assigned as follows:

Interface	Purpose
ether1	ISP 1 / WAN 1
ether2	ISP 2 / WAN 2

These interfaces were kept separate from:

bridge-LAN

because they are used for upstream internet connectivity rather than the internal LAN.

⸻

2. Connecting ISP 1

ISP 1 was connected physically to:

ether1

The connection path was:

ISP 1
  |
  v
ether1
  |
  v
MikroTik RB951Ui

⸻

3. Configuring WAN 1 DHCP Client

In WinBox, I navigated to:

IP
 ↓
DHCP Client

I created a DHCP client using:

Interface: ether1
Add Default Route: Yes
Use Peer DNS: Yes

After configuration, I waited for the DHCP client status to display:

Status: bound

This confirmed that ISP 1 had successfully assigned network configuration to the MikroTik.

⸻

4. Verifying ISP 1

Before configuring ISP 2, I tested the first WAN connection independently.

From:

WinBox
 ↓
New Terminal

I tested external connectivity using:

ping 8.8.8.8

I also tested internet access from the connected laptop.

The purpose of this test was to confirm that WAN 1 was working before introducing additional routing complexity.

⸻

5. Connecting ISP 2

ISP 2 was connected to:

ether2

The physical layout became:

ISP 1 ─── ether1
                \
                 MikroTik RB951Ui
                /
ISP 2 ─── ether2

At this point, the router had two physical upstream internet connections.

⸻

6. Configuring WAN 2 DHCP Client

I returned to:

IP
 ↓
DHCP Client

and created another DHCP client using:

Interface: ether2
Add Default Route: Yes
Use Peer DNS: Yes

I waited for the second WAN interface to display:

Status: bound

This confirmed that ISP 2 had also provided the MikroTik with network configuration.

⸻

7. Dual-WAN Physical Architecture

After connecting both ISPs, the network structure was:

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

At this stage, both WAN interfaces were physically connected and had received upstream configuration.

⸻

8. Independent ISP Testing

Before implementing PCC, I tested each ISP separately.

This was important because advanced load-balancing rules should not be introduced until both WAN connections work correctly on their own.

ISP 1 Test

ISP 2 was disconnected.

Expected traffic path:

LAN Device
    |
    v
 MikroTik
    |
    v
  ISP 1
    |
    v
 Internet

Internet connectivity was tested through ISP 1.

⸻

ISP 2 Test

ISP 1 was disconnected.

Expected traffic path:

LAN Device
    |
    v
 MikroTik
    |
    v
  ISP 2
    |
    v
 Internet

Internet connectivity was then tested through ISP 2.

⸻

9. Why Independent Testing Matters

Testing both ISPs separately helps isolate problems before configuring:

* PCC
* Connection marking
* Routing decisions
* Load balancing
* Automatic failover

If one WAN does not work independently, introducing PCC can make troubleshooting significantly more difficult.

The correct approach is:

WAN 1 working?
      ↓
     YES
WAN 2 working?
      ↓
     YES
Proceed to advanced routing

⸻

WAN Configuration Summary

Setting	WAN 1	WAN 2
Interface	ether1	ether2
Role	ISP 1	ISP 2
Addressing	DHCP Client	DHCP Client
Add Default Route	Yes	Yes
Use Peer DNS	Yes	Yes
Expected Status	bound	bound

⸻

Verification Criteria

The WAN configuration was considered successful when:

* ISP 1 was connected to ether1.
* ISP 2 was connected to ether2.
* Both DHCP clients displayed bound.
* ISP 1 provided internet access independently.
* ISP 2 provided internet access independently.

⸻

Next Stage

After both WAN interfaces were verified, the next stage was to configure NAT for each outbound WAN connection.

The following file documents that configuration:

docs/04-nat-configuration.md