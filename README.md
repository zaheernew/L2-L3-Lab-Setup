# IP Addressing Table

> Management IPs below follow the topology file (192.168.100.0/24). If your deployment uses a different mgmt subnet (e.g. 192.168.112.x / 192.168.113.x), substitute accordingly.

## Interface Addressing

| Device | Interface | IP Address | Connected To | Role |
|---|---|---|---|---|
| rou-1 | Ethernet0/0 | 192.168.100.10/24 | mgmt network | Mgmt (VRF clab-mgmt) |
| rou-1 | Ethernet0/1 | 10.0.12.1/30 | rou-2 Et0/1 | eBGP to customer A |
| rou-1 | Ethernet0/2 | 10.0.13.1/30 | rou-3 Et0/1 | eBGP to customer B |
| rou-1 | Loopback0 | 1.1.1.1/32 | — | Router ID |
| rou-2 | Ethernet0/0 | 192.168.100.20/24 | mgmt network | Mgmt (VRF clab-mgmt) |
| rou-2 | Ethernet0/1 | 10.0.12.2/30 | rou-1 Et0/1 | eBGP to ISP |
| rou-2 | Ethernet0/2 | 10.0.21.1/30 | fw-1 port2 | Link to site 1 firewall |
| rou-2 | Loopback0 | 2.2.2.2/32 | — | Router ID |
| rou-3 | Ethernet0/0 | 192.168.100.30/24 | mgmt network | Mgmt (VRF clab-mgmt) |
| rou-3 | Ethernet0/1 | 10.0.13.2/30 | rou-1 Et0/2 | eBGP to ISP |
| rou-3 | Ethernet0/2 | 10.0.31.1/30 | fw-2 port2 | Link to site 2 firewall |
| rou-3 | Loopback0 | 3.3.3.3/32 | — | Router ID |
| fw-1 | port1 | DHCP (10.0.0.x/24) | vrnetlab mgmt | Mgmt, external 192.168.100.51 |
| fw-1 | port2 | 10.0.21.2/30 | rou-2 Et0/2 | WAN |
| fw-1 | port3 | — | sw-1 Et0/1 | 802.1Q trunk parent |
| fw-1 | vlan10 (port3) | 10.100.1.1/24 | VLAN 10 | **pc-1 gateway** |
| fw-1 | vlan99 (port3) | 10.100.11.1/24 | VLAN 99 | sw-1 mgmt gateway |
| fw-2 | port1 | DHCP (10.0.0.x/24) | vrnetlab mgmt | Mgmt, external 192.168.100.52 |
| fw-2 | port2 | 10.0.31.2/30 | rou-3 Et0/2 | WAN |
| fw-2 | port3 | — | sw-2 Et0/1 | 802.1Q trunk parent |
| fw-2 | vlan20 (port3) | 10.100.2.1/24 | VLAN 20 | **pc-2 gateway** |
| fw-2 | vlan99 (port3) | 10.100.21.1/24 | VLAN 99 | sw-2 mgmt gateway |
| sw-1 | Ethernet0/0 | 192.168.100.41/24 | mgmt network | Mgmt (VRF clab-mgmt) |
| sw-1 | Ethernet0/1 | — | fw-1 port3 | Trunk (VLAN 10, 99) |
| sw-1 | Ethernet0/2 | — | pc-1 eth1 | Access VLAN 10 |
| sw-1 | Vlan99 | 10.100.11.2/24 | — | Switch mgmt SVI |
| sw-2 | Ethernet0/0 | 192.168.100.42/24 | mgmt network | Mgmt (VRF clab-mgmt) |
| sw-2 | Ethernet0/1 | — | fw-2 port3 | Trunk (VLAN 20, 99) |
| sw-2 | Ethernet0/2 | — | pc-2 eth1 | Access VLAN 20 |
| sw-2 | Vlan99 | 10.100.21.2/24 | — | Switch mgmt SVI |
| pc-1 | eth0 | 192.168.100.11/24 | mgmt network | Mgmt |
| pc-1 | eth1 | 10.100.1.10/24 | sw-1 Et0/2 | Static, gw 10.100.1.1 |
| pc-2 | eth0 | 192.168.100.12/24 | mgmt network | Mgmt |
| pc-2 | eth1 | 10.100.2.10/24 | sw-2 Et0/2 | Static, gw 10.100.2.1 |

## Subnet Summary

| Subnet | Purpose | Site |
|---|---|---|
| 10.0.12.0/30 | rou-1 ↔ rou-2 | ISP–A |
| 10.0.13.0/30 | rou-1 ↔ rou-3 | ISP–B |
| 10.0.21.0/30 | rou-2 ↔ fw-1 | Site 1 |
| 10.0.31.0/30 | rou-3 ↔ fw-2 | Site 2 |
| 10.100.1.0/24 | VLAN 10 – pc-1 users | Site 1 |
| 10.100.11.0/24 | VLAN 99 – sw-1 mgmt | Site 1 |
| 10.100.2.0/24 | VLAN 20 – pc-2 users | Site 2 |
| 10.100.21.0/24 | VLAN 99 – sw-2 mgmt | Site 2 |
| 192.168.100.0/24 | Containerlab management | All |

## BGP

| Router | AS | Role | Neighbor(s) | Advertised Networks |
|---|---|---|---|---|
| rou-1 | 65000 | ISP | 10.0.12.2 (AS 65002), 10.0.13.2 (AS 65003) | 1.1.1.1/32, 10.0.12.0/30, 10.0.13.0/30 |
| rou-2 | 65002 | Customer A | 10.0.12.1 (AS 65000) | 2.2.2.2/32, 10.0.12.0/30, 10.0.21.0/30, 10.100.1.0/24, 10.100.11.0/24 |
| rou-3 | 65003 | Customer B | 10.0.13.1 (AS 65000) | 3.3.3.3/32, 10.0.13.0/30, 10.0.31.0/30, 10.100.2.0/24, 10.100.21.0/24 |

## Static Routes

| Device | Destination | Next Hop | Purpose |
|---|---|---|---|
| rou-2 | 10.100.1.0/24 | 10.0.21.2 | Site 1 users via fw-1 |
| rou-2 | 10.100.11.0/24 | 10.0.21.2 | Site 1 mgmt via fw-1 |
| rou-3 | 10.100.2.0/24 | 10.0.31.2 | Site 2 users via fw-2 |
| rou-3 | 10.100.21.0/24 | 10.0.31.2 | Site 2 mgmt via fw-2 |
| fw-1 | 10.100.0.0/16 | 10.0.21.1 (port2) | Remote lab LANs |
| fw-1 | 10.0.0.0/16 | 10.0.21.1 (port2) | Lab transit links |
| fw-1 | 0.0.0.0/0 | 10.0.0.2 (port1, DHCP) | Mgmt – leave as is |
| fw-2 | 10.100.0.0/16 | 10.0.31.1 (port2) | Remote lab LANs |
| fw-2 | 10.0.0.0/16 | 10.0.31.1 (port2) | Lab transit links |
| fw-2 | 0.0.0.0/0 | 10.0.0.2 (port1, DHCP) | Mgmt – leave as is |
| sw-1 | 0.0.0.0/0 | 10.100.11.1 | Default via fw-1 |
| sw-2 | 0.0.0.0/0 | 10.100.21.1 | Default via fw-2 |

## Firewall Policies (fw-1; fw-2 identical with vlan20 instead of vlan10)

| ID | Name | Source Interface | Destination Interface | Action |
|---|---|---|---|---|
| 1 | LAN-to-WAN | vlan10, vlan99 | port2 | Accept, log all, no NAT |
| 2 | WAN-to-LAN | port2 | vlan10, vlan99 | Accept, log all, no NAT |
| 3 | INTER-VLAN | vlan10, vlan99 | vlan99, vlan10 | Accept, log all, no NAT |

## SSH Access from Host

| Device | Port | Credentials |
|---|---|---|
| fw-1 / fw-2 | 2221 / 2222 (GUI 1080 / 1081) | admin / admin |
| pc-1 / pc-2 | 2223 / 2224 | student / student |
| rou-1 / rou-2 / rou-3 | 2225 / 2226 / 2227 | admin / admin |
| sw-1 / sw-2 | 2228 / 2229 | admin / admin |
