Network Design

Overview

The purpose of this network design is to connect two independent Internet Service Providers to a MikroTik RB951Ui router while maintaining one internal LAN for connected devices.

The target architecture supports:

* Two WAN interfaces
* One bridged LAN
* DHCP for internal devices
* NAT for internet access
* PCC load balancing
* Automatic WAN failover

Physical Interface Assignment

Interface	Purpose
ether1	ISP 1 / WAN 1
ether2	ISP 2 / WAN 2
ether3	LAN
ether4	LAN
ether5	LAN

The WAN interfaces are intentionally kept separate from the LAN bridge.

Logical LAN Design

The LAN interfaces are grouped using a MikroTik bridge named:

bridge-LAN

The bridge contains:

ether3
ether4
ether5

The resulting LAN design is:

ether3 ──┐
ether4 ──┼── bridge-LAN
ether5 ──┘

LAN Addressing

The MikroTik router uses:

192.168.10.1/24

on bridge-LAN.

This creates the internal network:

Network: 192.168.10.0/24
Gateway: 192.168.10.1

Client devices receive addresses from this network using DHCP.

WAN Architecture

The two ISPs connect separately to the MikroTik:

ISP 1 ─── ether1
                \
                 MikroTik RB951Ui
                /
ISP 2 ─── ether2

Each WAN interface receives its upstream configuration using a DHCP client.

Complete Network Architecture

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

Design Goals

The completed architecture is intended to provide:

1. Dual ISP connectivity.
2. Internet access for one internal LAN.
3. DHCP-based client addressing.
4. NAT for both WAN connections.
5. PCC-based traffic distribution.
6. Automatic WAN failover.
7. Reduced dependence on a single ISP.

Important Design Note

PCC load balancing distributes separate connections between multiple WAN links.

For example:

Connection 1 → ISP 1
Connection 2 → ISP 2
Connection 3 → ISP 1
Connection 4 → ISP 2

Two 5 Mbps WAN links can therefore provide approximately 10 Mbps of aggregate network capacity across multiple connections.

This does not normally mean that one individual TCP connection or download will operate at 10 Mbps.

Current Implementation State

The basic network architecture has been established with:

* WAN 1 on ether1
* WAN 2 on ether2
* LAN on ether3–ether5
* bridge-LAN
* 192.168.10.1/24
* DHCP
* NAT for both WAN interfaces
* Independent ISP testing

PCC and automatic failover are the next stages of the project.