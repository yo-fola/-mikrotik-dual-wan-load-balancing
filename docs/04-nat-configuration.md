NAT Configuration

Overview

This section documents the Network Address Translation configuration used on the MikroTik RB951Ui.

The internal LAN uses private IP addresses from:

192.168.10.0/24

These private addresses are not directly routable on the public internet.

To allow LAN devices to access the internet through both ISPs, source NAT rules were configured using:

masquerade

A separate NAT rule was created for each WAN interface.

⸻

1. Why NAT Was Required

Devices on the internal LAN receive private addresses such as:

192.168.10.10
192.168.10.11
192.168.10.12

These devices use:

192.168.10.1

as their default gateway.

Before LAN traffic can leave through an ISP, the MikroTik must translate the private source address.

The traffic flow is approximately:

LAN Device
192.168.10.x
      |
      v
  MikroTik
      |
      v
Source NAT
      |
      v
WAN Interface
      |
      v
Internet

⸻

2. Configuring NAT for WAN 1

In WinBox, I navigated to:

IP
 ↓
Firewall
 ↓
NAT

I created a new NAT rule.

The rule was configured as:

Chain: srcnat
Out Interface: ether1

Under the Action tab:

Action: masquerade

This allows LAN devices to access the internet through ISP 1.

⸻

3. WAN 1 Traffic Flow

The resulting path was:

LAN Device
192.168.10.x
      |
      v
 bridge-LAN
      |
      v
  MikroTik
      |
      v
srcnat masquerade
      |
      v
   ether1
      |
      v
    ISP 1
      |
      v
   Internet

⸻

4. Configuring NAT for WAN 2

A second NAT rule was created for the second ISP.

I navigated again to:

IP
 ↓
Firewall
 ↓
NAT

The second rule was configured as:

Chain: srcnat
Out Interface: ether2

Under the Action tab:

Action: masquerade

This allows LAN devices to access the internet through ISP 2.

⸻

5. WAN 2 Traffic Flow

The resulting path was:

LAN Device
192.168.10.x
      |
      v
 bridge-LAN
      |
      v
  MikroTik
      |
      v
srcnat masquerade
      |
      v
   ether2
      |
      v
    ISP 2
      |
      v
   Internet

⸻

6. Final NAT Structure

After both rules were created, the router had separate outbound NAT handling for each WAN interface.

                    MikroTik
                       |
              +--------+--------+
              |                 |
              v                 v
      NAT via ether1     NAT via ether2
              |                 |
              v                 v
            ISP 1             ISP 2

The rules can be summarized as:

Chain	Out Interface	Action
srcnat	ether1	masquerade
srcnat	ether2	masquerade

⸻

7. Testing NAT

After configuring the NAT rule for WAN 1, I tested internet access through ISP 1.

I used:

ping 8.8.8.8

from the MikroTik terminal and also tested internet access from the connected laptop.

The same approach was used when testing ISP 2 independently.

The purpose of this testing was to confirm that:

* The WAN interface was connected.
* The ISP provided network configuration.
* The route was usable.
* NAT allowed LAN traffic to reach the internet.

⸻

8. Why Separate NAT Rules Were Used

Each ISP is connected through a different WAN interface:

ISP 1 → ether1
ISP 2 → ether2

Therefore, a separate masquerade rule was configured for each outbound interface.

This matches the dual-WAN architecture and prepares the router for later traffic distribution using PCC.

⸻

Configuration Summary

Item	WAN 1	WAN 2
Interface	ether1	ether2
NAT Chain	srcnat	srcnat
Action	masquerade	masquerade
Purpose	Internet access through ISP 1	Internet access through ISP 2

⸻

Verification Result

The NAT configuration was considered successful when LAN devices could access the internet through each ISP during independent testing.

At this stage, the router had:

* A working LAN
* DHCP
* Two WAN interfaces
* NAT for WAN 1
* NAT for WAN 2
* Independent ISP connectivity

⸻

Next Stage

The next stage is:

docs/05-pcc-load-balancing.md

This will document the purpose and planned implementation of Per Connection Classifier load balancing.