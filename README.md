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


# L2/L3 Lab Configurations


### Configurations

All the command-let are here, We recomend using PUTTY for this Lab setup.

* Visit to '[http/10.72.96.14:8080](http://10.72.96.14:8080/) put in your given student NO and press the *Get Access* button to get your logins.
plese remember for all the device logins are:

username: admin

password: password

* except for PC use 

    Username: student

    password: student

## Router 1
--

```
enable
configure terminal
!
interface Loopback0
 description ROUTER-ID
 ip address 1.1.1.1 255.255.255.255
!
interface Ethernet0/1
 description eBGP-to-CUSTOMER-rou-2
 ip address 10.0.12.1 255.255.255.252
 no shutdown
!
interface Ethernet0/2
 description eBGP-to-CUSTOMER-rou-3
 ip address 10.0.13.1 255.255.255.252
 no shutdown
```

###### BGP Config
```

router bgp 65000
 bgp router-id 1.1.1.1
 bgp log-neighbor-changes
 neighbor 10.0.12.2 remote-as 65002
 neighbor 10.0.12.2 description CUSTOMER-rou-2
 neighbor 10.0.13.2 remote-as 65003
 neighbor 10.0.13.2 description CUSTOMER-rou-3
 !
 address-family ipv4 unicast
  network 1.1.1.1 mask 255.255.255.255
  network 10.0.12.0 mask 255.255.255.252
  network 10.0.13.0 mask 255.255.255.252
  neighbor 10.0.12.2 activate
  neighbor 10.0.13.2 activate
 exit-address-family
!
end
write memory
```
## Router 2


```
enable
configure terminal
!
interface Loopback0
 description ROUTER-ID
 ip address 2.2.2.2 255.255.255.255
!
interface Ethernet0/1
 description eBGP-to-ISP-rou-1
 ip address 10.0.12.2 255.255.255.252
 no shutdown
!
interface Ethernet0/2
 description to-fw-1-port2
 ip address 10.0.21.1 255.255.255.252
 no shutdown
```
###### static route

```
! Site-1 LANs live behind fw-1
ip route 10.100.1.0 255.255.255.0 10.0.21.2 name PC1-LAN-via-fw-1
ip route 10.100.11.0 255.255.255.0 10.0.21.2 name SW1-MGMT-via-fw-1
!
```
###### BGP Config

```
router bgp 65002
 bgp router-id 2.2.2.2
 bgp log-neighbor-changes
 neighbor 10.0.12.1 remote-as 65000
 neighbor 10.0.12.1 description ISP-rou-1
 !
 address-family ipv4 unicast
  network 2.2.2.2 mask 255.255.255.255
  network 10.0.12.0 mask 255.255.255.252
  network 10.0.21.0 mask 255.255.255.252
  network 10.100.1.0 mask 255.255.255.0
  network 10.100.11.0 mask 255.255.255.0
  neighbor 10.0.12.1 activate
 exit-address-family
!
end
write memory
```

## Router 3

```
enable
configure terminal
!
interface Loopback0
 description ROUTER-ID
 ip address 3.3.3.3 255.255.255.255
!
interface Ethernet0/1
 description eBGP-to-ISP-rou-1
 ip address 10.0.13.2 255.255.255.252
 no shutdown
!
interface Ethernet0/2
 description to-fw-2-port2
 ip address 10.0.31.1 255.255.255.252
 no shutdown
!
```
###### static route

```
! Site-2 LANs live behind fw-2
ip route 10.100.2.0 255.255.255.0 10.0.31.2 name PC2-LAN-via-fw-2
ip route 10.100.21.0 255.255.255.0 10.0.31.2 name SW2-MGMT-via-fw-2
!
```

###### BGP Config

```
router bgp 65003
 bgp router-id 3.3.3.3
 bgp log-neighbor-changes
 neighbor 10.0.13.1 remote-as 65000
 neighbor 10.0.13.1 description ISP-rou-1
 !
 address-family ipv4 unicast
  network 3.3.3.3 mask 255.255.255.255
  network 10.0.13.0 mask 255.255.255.252
  network 10.0.31.0 mask 255.255.255.252
  network 10.100.2.0 mask 255.255.255.0
  network 10.100.21.0 mask 255.255.255.0
  neighbor 10.0.13.1 activate
 exit-address-family
!
end
write memory
```

# Firewalls


##### Web portal ports to access to Firewall

## FortiGate Lab Port Allocation

| Student Lab No | FW 1 Web Dashboard | FW 1 SSH | FW 2 Web Dashboard | FW 2 SSH |
|:------:|-------------------:|---------:|-------------------:|---------:|
| 01 | 1080 | 2221 | 1081 | 2222 |
| 02 | 2080 | 2241 | 2081 | 2242 |
| 03 | 3080 | 2261 | 3081 | 2262 |
| 04 | 4080 | 2281 | 4081 | 2282 |
| 05 | 5080 | 2301 | 5081 | 2302 |
| 06 | 6080 | 2321 | 6081 | 2322 |
| 07 | 7080 | 2341 | 7081 | 2342 |
| 08 | 8090 | 2361 | 8091 | 2362 |
| 09 | 9080 | 2381 | 9081 | 2382 |
| 10 | 1082 | 2401 | 1083 | 2402 |
| 11 | 1180 | 2421 | 1181 | 2422 |
| 12 | 1280 | 2441 | 1281 | 2442 |
| 13 | 1380 | 2461 | 1381 | 2462 |
| 14 | 1480 | 2481 | 1481 | 2482 |
| 15 | 1580 | 2501 | 1581 | 2502 |
| 16 | 1680 | 2521 | 1681 | 2522 |
| 17 | 1780 | 2541 | 1781 | 2542 |
| 18 | 1880 | 2561 | 1881 | 2562 |
| 19 | 1980 | 2581 | 1981 | 2582 |
| 20 | 2082 | 2601 | 2083 | 2602 |
| 21 | 2180 | 2621 | 2181 | 2622 |
| 22 | 2380 | 2641 | 2381 | 2642 |
| 23 | 2480 | 2661 | 2481 | 2662 |
| 24 | 2580 | 2681 | 2581 | 2682 |




## Firewall 1

###### configure firewall interfaces

```
config system global
    set hostname "fw-1"
end

config system interface
    edit "port2"
        set mode static
        set ip 10.0.21.2 255.255.255.252
        set allowaccess ping
        set alias "WAN-to-rou-2"
        set role wan
    next
    edit "port3"
        set alias "TRUNK-to-sw-1"
        set allowaccess ping
    next
    edit "vlan10"
        set vdom "root"
        set interface "port3"
        set vlanid 10
        set ip 10.100.1.1 255.255.255.0
        set allowaccess ping
        set alias "PC1-USERS"
        set role lan
    next
    edit "vlan99"
        set vdom "root"
        set interface "port3"
        set vlanid 99
        set ip 10.100.11.1 255.255.255.0
        set allowaccess ping
        set alias "SW1-MGMT"
        set role lan
    next
end

```

###### Configure Static routes 

```
config router static
    edit 1
        set dst 10.100.0.0 255.255.0.0
        set gateway 10.0.21.1
        set device "port2"
        set comment "lab LANs via rou-2"
    next
    edit 2
        set dst 10.0.0.0 255.255.0.0
        set gateway 10.0.21.1
        set device "port2"
        set comment "lab transit links via rou-2"
    next
end
```

###### Firewall policy

```
config firewall policy
    edit 1
        set name "LAN-to-WAN"
        set srcintf "vlan10"
        set dstintf "port2"
        set srcaddr "all"
        set dstaddr "all"
        set action accept
        set schedule "always"
        set service "ALL"
        set logtraffic all
        set nat disable
    next
    edit 2
        set name "WAN-to-LAN"
        set srcintf "port2"
        set dstintf "vlan10"
        set srcaddr "all"
        set dstaddr "all"
        set action accept
        set schedule "always"
        set service "ALL"
        set logtraffic all
        set nat disable
    next
    edit 3
        set name "INTER-VLAN"
        set srcintf "vlan10" "vlan99"
        set dstintf "vlan99" "vlan10"
        set srcaddr "all"
        set dstaddr "all"
        set action accept
        set schedule "always"
        set service "ALL"
        set logtraffic all
        set nat disable
    next
end
```

###### enable firewall log traffic

```
config log memory setting
    set status enable
end
```

## Firewall 2


###### configure firewall interfaces
```
config system global
    set hostname "fw-2"
end

config system interface
    edit "port2"
        set mode static
        set ip 10.0.31.2 255.255.255.252
        set allowaccess ping
        set alias "WAN-to-rou-3"
        set role wan
    next
    edit "port3"
        set alias "TRUNK-to-sw-2"
        set allowaccess ping
    next
    edit "vlan20"
        set vdom "root"
        set interface "port3"
        set vlanid 20
        set ip 10.100.2.1 255.255.255.0
        set allowaccess ping
        set alias "PC2-USERS"
        set role lan
    next
    edit "vlan99"
        set vdom "root"
        set interface "port3"
        set vlanid 99
        set ip 10.100.21.1 255.255.255.0
        set allowaccess ping
        set alias "SW2-MGMT"
        set role lan
    next
end
```
###### Configure Static routes 

```
config router static
    edit 1
        set dst 10.100.0.0 255.255.0.0
        set gateway 10.0.31.1
        set device "port2"
        set comment "lab LANs via rou-3"
    next
    edit 2
        set dst 10.0.0.0 255.255.0.0
        set gateway 10.0.31.1
        set device "port2"
        set comment "lab transit links via rou-3"
    next
end
```
###### enable firewall log traffic

```
config log memory setting
    set status enable
end
```
###### Firewall policy

```

config firewall policy
    edit 1
        set name "LAN-to-WAN"
        set srcintf "vlan20"
        set dstintf "port2"
        set srcaddr "all"
        set dstaddr "all"
        set action accept
        set schedule "always"
        set service "ALL"
        set logtraffic all
        set nat disable
    next
    edit 2
        set name "WAN-to-LAN"
        set srcintf "port2"
        set dstintf "vlan20"
        set srcaddr "all"
        set dstaddr "all"
        set action accept
        set schedule "always"
        set service "ALL"
        set logtraffic all
        set nat disable
    next
    edit 3
        set name "INTER-VLAN"
        set srcintf "vlan20" "vlan99"
        set dstintf "vlan99" "vlan20"
        set srcaddr "all"
        set dstaddr "all"
        set action accept
        set schedule "always"
        set service "ALL"
        set logtraffic all
        set nat disable
    next
end
```

## Switch 1

```
enable
configure terminal
!
vlan 10
 name PC1-USERS
vlan 99
 name MGMT
exit
!
! TRUNK port to fw-1 (router-on-a-stick on the FortiGate)
interface Ethernet0/1
 description TRUNK-to-fw-1-port3
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 10,99
 switchport nonegotiate
 no shutdown
!
! ACCESS port to pc-1
interface Ethernet0/2
 description ACCESS-pc-1
 switchport mode access
 switchport access vlan 10
 spanning-tree portfast
 no shutdown
!
! Switch management SVI in VLAN 99 (proves inter-VLAN routing via fw-1)
interface Vlan99
 description SW-MGMT
 ip address 10.100.11.2 255.255.255.0
 no shutdown
!
ip default-gateway 10.100.11.1
! if the image has "ip routing" enabled, ip default-gateway is ignored, so also:
ip route 0.0.0.0 0.0.0.0 10.100.11.1
!
end
write memory
```
## Switch 2

```
enable
configure terminal
!
vlan 20
 name PC2-USERS
vlan 99
 name MGMT
exit
!
! TRUNK port to fw-2
interface Ethernet0/1
 description TRUNK-to-fw-2-port3
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 20,99
 switchport nonegotiate
 no shutdown
!
! ACCESS port to pc-2
interface Ethernet0/2
 description ACCESS-pc-2
 switchport mode access
 switchport access vlan 20
 spanning-tree portfast
 no shutdown
!
interface Vlan99
 description SW-MGMT
 ip address 10.100.21.2 255.255.255.0
 no shutdown
!
ip default-gateway 10.100.21.1
ip route 0.0.0.0 0.0.0.0 10.100.21.1
!
end
write memory

```
#### Final verifications and troubleshooting


* IXMP packet capture in the firewall

```
diagnose sniffer packet any 'icmp' 4 0 1
```

* Show all arouting in the firewall

```
get router info routing-table all
```
* Web traffic on firewall

    check with and traceroute

    check with ping
  
```
ping 10.100.2.10
traceroute 10.100.2.10
```
