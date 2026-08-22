WAN Failover

Overview

This section documents the planned automatic WAN failover design for the MikroTik RB951Ui dual-WAN network.

The network uses:

WAN 1 → ether1 → ISP 1
WAN 2 → ether2 → ISP 2

The objective of failover is to maintain internet connectivity when one ISP becomes unavailable.

⸻

Why Failover Is Required

Using two ISPs provides redundancy only if the router can detect when one connection is no longer usable and route traffic through the remaining WAN.

Simply connecting two ISPs does not automatically provide failover.

The router must make routing decisions based on WAN availability.

⸻

Normal Operation

When both ISPs are functioning, the intended design is:

ISP 1 ✓            ISP 2 ✓
   \                  /
    \                /
      PCC Load Balancing
             |
          MikroTik
             |
         bridge-LAN
             |
           Clients

During normal operation, PCC is intended to distribute connections across both WAN links.

⸻

ISP 1 Failure

If ISP 1 becomes unavailable:

ISP 1 ✕
ISP 2 ✓
   |
   v
MikroTik
   |
   v
LAN Clients

Usable internet traffic should continue through ISP 2.

The network should remain online instead of losing connectivity completely.

⸻

ISP 2 Failure

If ISP 2 becomes unavailable:

ISP 1 ✓
   |
   v
MikroTik
   |
   v
LAN Clients
ISP 2 ✕

Traffic should continue through ISP 1.

⸻

Intended Failover Logic

The intended behavior can be summarized as:

Are both WANs available?
        |
       YES
        |
        v
Use PCC load balancing

If WAN 1 fails:

WAN 1 unavailable
        |
        v
Use WAN 2

If WAN 2 fails:

WAN 2 unavailable
        |
        v
Use WAN 1

⸻

Failover Requirements

The completed failover implementation is expected to require:

* Separate WAN routes
* WAN health checking
* Route preference or distance
* PCC-aware routing
* Correct NAT behavior
* Detection of a failed WAN path
* Automatic selection of the remaining usable route

The exact RouterOS configuration will be added after the final implementation has been verified.

⸻

Why Health Checking Matters

A physical Ethernet interface being connected does not necessarily mean the internet connection is usable.

For example:

ether1 = link up
ISP 1 = internet unavailable

A complete failover design should therefore determine whether the actual WAN path is usable, rather than relying only on whether the cable is connected.

The final health-check method will be documented once implemented.

⸻

Relationship Between PCC and Failover

The intended architecture combines two functions:

PCC
+
Failover

PCC handles traffic distribution while both links are healthy.

Failover handles connectivity when one WAN becomes unavailable.

Conceptually:

                  ISP 1
                    |
                    |
                    +----+
                         |
                      MikroTik
                         |
                    PCC Routing
                         |
                    +----+
                    |
                  ISP 2

Normal operation:

WAN 1 ✓ + WAN 2 ✓
        |
        v
       PCC

Failure condition:

WAN 1 ✕ + WAN 2 ✓
        |
        v
      WAN 2

or:

WAN 1 ✓ + WAN 2 ✕
        |
        v
      WAN 1

⸻

Failover Testing Plan

The final configuration should be tested deliberately rather than assumed to work.

Test 1 — Both ISPs Online

Verify:

* WAN 1 has internet access.
* WAN 2 has internet access.
* LAN clients have internet access.
* PCC distributes connections across both WAN paths.

⸻

Test 2 — Simulate WAN 1 Failure

Disconnect or disable WAN 1.

Expected result:

WAN 1 ✕
WAN 2 ✓

Verify that:

* Internet access remains available.
* Traffic uses WAN 2.
* LAN clients do not require manual router reconfiguration.

⸻

Test 3 — Restore WAN 1

Reconnect or re-enable WAN 1.

Verify that:

* WAN 1 becomes usable again.
* The router returns to the intended dual-WAN behavior.

⸻

Test 4 — Simulate WAN 2 Failure

Disconnect or disable WAN 2.

Expected result:

WAN 1 ✓
WAN 2 ✕

Verify that:

* Internet access remains available.
* Traffic uses WAN 1.
* LAN devices remain connected.

⸻

Test 5 — Restore WAN 2

Reconnect or re-enable WAN 2.

Verify that both WAN links become available again.

⸻

Troubleshooting Sequence

If failover does not work correctly, the configuration should be checked in this order:

WAN physical connection
        ↓
WAN DHCP status
        ↓
Gateway availability
        ↓
WAN route
        ↓
NAT
        ↓
Health checking
        ↓
Routing decision
        ↓
PCC interaction
        ↓
LAN internet access

This reduces unnecessary configuration changes and makes it easier to identify the actual failure point.

⸻

Evidence to Add

When the final failover configuration is completed, evidence should include:

evidence/
├── 12-wan-routes.png
├── 13-health-check.png
├── 14-wan1-failure-test.png
├── 15-wan2-failure-test.png
└── 16-failover-restoration.png

Where necessary, sensitive ISP information should be hidden before screenshots are uploaded publicly.

⸻

Current Status

Completed

* Dual WAN interfaces configured
* Both WAN DHCP clients configured
* NAT for both WAN interfaces
* Independent WAN testing
* Failover behavior defined

Pending

* Final WAN health-check configuration
* Route preference configuration
* Automatic failover rules
* WAN 1 failure test
* WAN 2 failure test
* Recovery testing
* Evidence screenshots

⸻

Expected Final Result

The final network should behave as follows:

Both ISPs working
       |
       v
PCC Load Balancing
       |
       v
      LAN

If one ISP fails:

One WAN unavailable
       |
       v
Remaining WAN
       |
       v
      LAN

The purpose of this design is to reduce dependence on a single ISP and improve network availability.