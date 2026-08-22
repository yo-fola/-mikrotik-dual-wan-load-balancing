Evidence

This directory will contain screenshots and validation evidence for the MikroTik Dual-WAN Load Balancing & Failover project.

The evidence will be added after the final PCC and failover configuration is completed and tested.

Planned Evidence

evidence/
├── 01-interface-layout.png
├── 02-lan-bridge.png
├── 03-lan-ip-address.png
├── 04-dhcp-server.png
├── 05-wan1-dhcp-bound.png
├── 06-wan2-dhcp-bound.png
├── 07-nat-rules.png
├── 08-wan1-connectivity-test.png
├── 09-wan2-connectivity-test.png
├── 10-pcc-mangle-rules.png
├── 11-connection-marks.png
├── 12-routing-configuration.png
├── 13-wan-health-check.png
├── 14-wan1-failover-test.png
├── 15-wan2-failover-test.png
└── 16-dual-wan-restored.png

Evidence Guidelines

Each screenshot should demonstrate one specific part of the implementation.

Examples:

Interface Layout

Show the MikroTik interfaces and their assigned roles:

ether1 = WAN 1
ether2 = WAN 2
ether3 = LAN
ether4 = LAN
ether5 = LAN

LAN Bridge

Show:

bridge-LAN

with:

ether3
ether4
ether5

added as bridge ports.

LAN IP Address

Show the router LAN address:

192.168.10.1/24

assigned to:

bridge-LAN

DHCP

Show the DHCP server configured on:

bridge-LAN

WAN DHCP

Show both WAN interfaces with DHCP client status:

bound

NAT

Show the two source NAT masquerade rules for:

ether1
ether2

PCC

After PCC is completed, show the final mangle and connection-marking configuration.

Routing

Show the routing configuration used to direct classified traffic through the correct WAN.

Failover

Capture evidence showing that internet connectivity remains available when either WAN connection is disconnected or disabled.

Security Before Uploading

Before publishing screenshots, review them for sensitive information.

Do not expose:

* Router passwords
* Administrative credentials
* ISP authentication details
* VPN secrets
* Wireless passwords
* Private keys
* API keys
* Session tokens
* Any credentials stored in comments or configuration fields

Where necessary, blur or crop sensitive values before uploading the image.

Evidence Status

Current documentation already covers:

* LAN bridge
* LAN addressing
* DHCP
* WAN 1 configuration
* WAN 2 configuration
* NAT
* Independent WAN testing

The remaining screenshots will be added after PCC and automatic failover are fully configured and validated.