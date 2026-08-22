LAN Configuration

Overview

This section documents the configuration of the internal Local Area Network (LAN) on the MikroTik RB951Ui.

The LAN was designed to use three physical Ethernet interfaces as one logical network.

The interfaces used were:

ether3
ether4
ether5

These interfaces were grouped using a MikroTik bridge named:

bridge-LAN

The router was then assigned the LAN gateway address:

192.168.10.1/24

A DHCP server was also configured so connected devices could automatically receive IP addresses.

⸻

1. Connecting to the MikroTik

I connected my laptop to one of the MikroTik LAN interfaces, preferably:

ether3

I then opened:

WinBox

From the Neighbors tab, I located the MikroTik router and connected using its MAC address.

This allowed me to access the router before the LAN IP configuration was completed.

⸻

2. Interface Assignment

The router interfaces were assigned as follows:

Interface	Purpose
ether1	ISP 1 / WAN 1
ether2	ISP 2 / WAN 2
ether3	LAN
ether4	LAN
ether5	LAN

Only the LAN interfaces were added to the bridge.

The WAN interfaces remained separate.

⸻

3. Creating the LAN Bridge

In WinBox, I navigated to:

Bridge

I created a new bridge with the name:

bridge-LAN

The purpose of this bridge was to group multiple physical Ethernet interfaces into one logical LAN.

⸻

4. Adding LAN Interfaces to the Bridge

I navigated to:

Bridge
    ↓
Ports

I added the following interfaces to:

bridge-LAN
ether3
ether4
ether5

The resulting logical structure was:

ether3 ──┐
ether4 ──┼── bridge-LAN
ether5 ──┘

ether1 and ether2 were not added because they were reserved for the two WAN connections.

⸻

5. Configuring the LAN Gateway

I navigated to:

IP
 ↓
Addresses

I added:

Address: 192.168.10.1/24
Interface: bridge-LAN

This configured the MikroTik router as the gateway for the internal network.

The resulting LAN addressing was:

Network: 192.168.10.0/24
Gateway: 192.168.10.1

⸻

6. Configuring DHCP

To automatically assign IP addresses to LAN devices, I opened:

IP
 ↓
DHCP Server
 ↓
DHCP Setup

I selected:

Interface: bridge-LAN

The LAN configuration used:

Network: 192.168.10.0/24
Gateway: 192.168.10.1

The DHCP server could then provide addresses such as:

192.168.10.10
192.168.10.11
192.168.10.12

to connected devices.

⸻

7. LAN Verification

After completing the bridge and DHCP configuration, I verified that my connected laptop received an IP address in the:

192.168.10.x

range.

This confirmed that:

* bridge-LAN was functioning.
* The LAN ports were correctly grouped.
* The router LAN IP was configured.
* DHCP was assigning addresses to connected devices.

⸻

LAN Traffic Flow

The resulting LAN design was:

Laptop / Client
      |
      v
ether3 / ether4 / ether5
      |
      v
 bridge-LAN
      |
      v
192.168.10.1
 MikroTik

At this stage, the internal network was functioning independently of the WAN configuration.

⸻

Configuration Summary

Setting	Value
LAN Bridge	bridge-LAN
LAN Ports	ether3, ether4, ether5
Router LAN IP	192.168.10.1/24
Network	192.168.10.0/24
Default Gateway	192.168.10.1
Address Assignment	DHCP

⸻

Verification Result

The LAN configuration was considered successful when the connected laptop received an address in the:

192.168.10.x

range.

This provided the foundation required before configuring the two WAN connections.