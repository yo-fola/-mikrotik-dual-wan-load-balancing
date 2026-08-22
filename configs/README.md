Configuration Files

This directory is reserved for sanitized MikroTik RouterOS configuration exports and configuration examples related to the dual-WAN project.

The goal is to preserve the technical configuration used in the project without exposing sensitive production information.

Planned Files

configs/
├── README.md
├── routeros-export-sanitized.rsc
├── pcc-rules-example.rsc
└── failover-routes-example.rsc

Main RouterOS Export

The final project should include a sanitized RouterOS export such as:

routeros-export-sanitized.rsc

This file can be generated from the MikroTik terminal using:

/export hide-sensitive file=routeros-export

The generated file should be reviewed manually before it is uploaded to GitHub.

Important Security Check

Even when hide-sensitive is used, the export should still be reviewed before publication.

Remove or replace anything that reveals:

* Public IP addresses
* ISP-specific information
* Router administrator usernames
* Passwords
* Wireless credentials
* VPN secrets
* Private keys
* Authentication tokens
* Organization-specific internal information

Example Sanitization

Instead of publishing environment-specific information such as:

gateway=192.0.2.10

a public documentation example can use:

gateway=<WAN1_GATEWAY>

Similarly:

WAN1_GATEWAY
WAN2_GATEWAY
ISP1_ADDRESS
ISP2_ADDRESS

can be used where revealing the actual infrastructure is unnecessary.

Configuration Areas to Preserve.

The sanitized export should eventually demonstrate the parts of the project related to:

* Interface configuration
* bridge-LAN
* Bridge ports
* LAN addressing
* DHCP
* WAN DHCP clients
* NAT
* PCC mangle rules
* Connection marks
* Routing configuration
* WAN health checks
* Failover routes

Current Status

The project documentation currently supports the following configuration areas:

bridge-LAN
LAN addressing
DHCP
WAN 1 DHCP client
WAN 2 DHCP client
WAN 1 masquerade
WAN 2 masquerade

The final PCC and failover configuration files will be added after those stages are fully implemented and verified.

Why the Configuration Export Matters

The screenshots in the evidence/ directory show that the configuration exists visually.

The RouterOS export provides a text-based technical record of how the router was configured.

Together, they provide stronger project evidence:

Documentation
      +
Screenshots
      +
Sanitized Configuration
      =
Reproducible Portfolio Project