Network Diagrams

This directory is reserved for visual diagrams that explain the MikroTik Dual-WAN Load Balancing & Failover architecture.

The diagrams will make the project easier to understand without requiring the reader to inspect every RouterOS setting.

Planned Diagrams

diagrams/
├── README.md
├── 01-network-architecture.png
├── 02-lan-bridge-design.png
├── 03-pcc-traffic-flow.png
└── 04-failover-flow.png

1. Network Architecture Diagram

The main diagram should represent the overall environment:

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

This diagram should clearly show the separation between WAN and LAN interfaces.

⸻

2. LAN Bridge Diagram

The LAN bridge diagram should show:

ether3 ──┐
ether4 ──┼── bridge-LAN
ether5 ──┘
            |
            v
      192.168.10.1/24

This explains how the three physical LAN ports operate as one logical internal network.

⸻

3. PCC Traffic Flow Diagram

After PCC is fully configured, the diagram should show how separate connections are distributed across both WAN links.

Example:

LAN Clients
     |
     v
  MikroTik
     |
     v
    PCC
   /   \
  /     \
 v       v
ISP 1   ISP 2

The diagram should make it clear that PCC distributes connections rather than combining every packet into one larger connection.

⸻

4. Failover Diagram

The failover diagram should show both normal and failure states.

Normal Operation

ISP 1 ✓      ISP 2 ✓
   \          /
    \        /
      MikroTik
         |
        LAN

ISP 1 Failure

ISP 1 ✕      ISP 2 ✓
                |
             MikroTik
                |
               LAN

ISP 2 Failure

ISP 1 ✓      ISP 2 ✕
   |
MikroTik
   |
  LAN

⸻

Diagram Guidelines

The final diagrams should be:

* Simple
* Clearly labeled
* Technically accurate
* Easy to understand
* Free of sensitive production information

Avoid exposing unnecessary public IP addresses, ISP identifiers, or credentials.

Current Status

The text-based network architecture has already been documented.

Graphical versions will be added after the PCC and failover implementation is fully completed and validated.