Testing and Troubleshooting

Overview

This section documents the testing and troubleshooting approach used during the MikroTik RB951Ui dual-WAN project.

The main principle was:

Test each part of the network independently before introducing more advanced routing.

This made it easier to determine whether a problem was related to:

* LAN configuration
* DHCP
* WAN connectivity
* NAT
* Routing
* PCC
* Failover

⸻

Troubleshooting Methodology

The network was tested in stages.

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

Each stage should work before moving to the next.

⸻

1. LAN Testing

After configuring bridge-LAN, I verified that the connected laptop received an address in the:

192.168.10.x

range.

The expected LAN configuration was:

Network: 192.168.10.0/24
Gateway: 192.168.10.1

If the laptop received a valid address from this network, it confirmed that:

* bridge-LAN was active
* LAN ports were correctly assigned
* DHCP was working
* The client could communicate with the MikroTik LAN

⸻

2. WAN DHCP Testing

Each WAN interface was configured as a DHCP client.

For WAN 1:

Interface: ether1
Expected Status: bound

For WAN 2:

Interface: ether2
Expected Status: bound

A bound status confirmed that the MikroTik had received network configuration from the upstream ISP.

⸻

3. ISP 1 Connectivity Test

ISP 1 was tested independently before introducing ISP 2.

From:

WinBox
 ↓
New Terminal

I used:

ping 8.8.8.8

The purpose of this test was to confirm that the MikroTik could reach an external IP address through ISP 1.

Internet access was also tested from the connected laptop.

⸻

4. ISP 2 Connectivity Test

ISP 2 was also tested independently.

ISP 1 was disconnected so that ISP 2 could be tested without interference from the first WAN connection.

Expected traffic path:

Laptop
   |
   v
MikroTik
   |
   v
ISP 2
   |
   v
Internet

This confirmed whether ISP 2 could provide internet connectivity on its own.

⸻

5. NAT Verification

Each WAN interface had its own masquerade rule.

WAN 1:

Chain: srcnat
Out Interface: ether1
Action: masquerade

WAN 2:

Chain: srcnat
Out Interface: ether2
Action: masquerade

If the MikroTik itself could access the internet but LAN devices could not, NAT would be one of the areas to check.

⸻

6. Independent WAN Testing

Before configuring PCC, both ISPs were tested separately.

The testing process was:

Test WAN 1 alone
      ↓
Confirm internet access
      ↓
Test WAN 2 alone
      ↓
Confirm internet access
      ↓
Proceed to PCC

This helped avoid introducing multiple possible failure points at the same time.

⸻

7. Troubleshooting Order

When internet connectivity does not work, the following order should be checked:

Step 1 — Physical Connection

Confirm that:

* ISP 1 is connected to ether1
* ISP 2 is connected to ether2
* LAN devices are connected to ether3, ether4, or ether5

⸻

Step 2 — LAN Configuration

Confirm:

bridge-LAN

contains:

ether3
ether4
ether5

Also confirm:

192.168.10.1/24

is assigned to bridge-LAN.

⸻

Step 3 — DHCP

Verify that LAN clients receive addresses in the:

192.168.10.x

range.

⸻

Step 4 — WAN DHCP

Verify that the DHCP client on each WAN interface displays:

Status: bound

If the interface is not bound, the router may not have received valid upstream configuration.

⸻

Step 5 — NAT

Verify that both WAN interfaces have masquerade rules.

ether1 → masquerade
ether2 → masquerade

⸻

Step 6 — Internet Connectivity

Test from the MikroTik terminal:

ping 8.8.8.8

Then test internet access from a LAN device.

⸻

8. PCC Troubleshooting Plan

After PCC is implemented, troubleshooting should include checking:

Connection classification
        ↓
Connection marks
        ↓
Routing marks / tables
        ↓
WAN route selection
        ↓
NAT
        ↓
Internet access

The actual PCC rule validation will be added after the final configuration is completed.

⸻

9. Failover Testing Plan

The failover configuration should be tested deliberately.

Test WAN 1 Failure

Disable or disconnect WAN 1.

Expected result:

WAN 1 ✕
WAN 2 ✓

Internet access should remain available through WAN 2.

⸻

Test WAN 2 Failure

Disable or disconnect WAN 2.

Expected result:

WAN 1 ✓
WAN 2 ✕

Internet access should remain available through WAN 1.

⸻

Restore Both WANs

After restoring both connections, the router should return to normal dual-WAN operation.

⸻

10. Evidence to Collect

The following screenshots should be collected when the final project evidence is added:

evidence/
├── 01-interface-layout.png
├── 02-lan-bridge.png
├── 03-dhcp-server.png
├── 04-wan1-bound.png
├── 05-wan2-bound.png
├── 06-nat-rules.png
├── 07-wan1-test.png
├── 08-wan2-test.png
├── 09-pcc-rules.png
├── 10-routing-rules.png
├── 11-wan1-failover-test.png
└── 12-wan2-failover-test.png

⸻

Key Troubleshooting Lesson

The most important troubleshooting principle from this project is to avoid changing several settings at once.

Instead:

Identify the failing stage
        ↓
Test that stage
        ↓
Correct the issue
        ↓
Retest
        ↓
Move to the next stage

This makes troubleshooting more precise and reduces unnecessary configuration changes.

⸻

Current Status

Verified

* LAN bridge
* LAN addressing
* DHCP
* WAN 1 DHCP client
* WAN 2 DHCP client
* NAT on WAN 1
* NAT on WAN 2
* Independent WAN testing

To Be Verified

* PCC traffic distribution
* Routing marks / routing tables
* WAN health checks
* WAN 1 failover
* WAN 2 failover
* Automatic recovery