# Enterprise Network with High Availability and Security
A **practical** implementation of CCNA concepts to design a real-world enterprise network.
> For detailed study notes and foundational concepts applied in this project, please refer to my [CCNA Learning Repository](https://github.com/sangurai/CCNA).


## Technologies
* Cisco Packet Tracer


## Networking Skills Applied

### 1. Network Foundation & IP Addressing
#### Subnetting (VLSM) & IP Address Allocation Convention
To ensure systematic management and ease of troubleshooting, the IP allocation follows these conventions:
* **1st Usable IP:** Assigned as the **Virtual Router IP** for HSRP (acting as the Default Gateway for hosts).
* **Next Usable IPs:** Assigned as the **Physical IPs** on the router's sub-interfaces.
* **Remaining IPs:** Allocated to the end devices (hosts) within the subnet.

#### LAN Subnetting
> *For detailed subnetting calculations, please refer to my [Handwritten Draft PDF](handwrittenDraft.pdf)*

**Major Network Assigned:** `172.16.0.0/16`
| VLAN | Department | Hosts | Prefix | Network Address | Virtual IP (HSRP) | First Usable | Last Usable | Broadcast Address |
| :--- | :--- | :---: | :---: | :--- | :--- | :--- | :--- | :--- |
| **VLAN 50** | Guests | 200 | `/24` | `172.16.0.0` | `172.16.0.1` | `172.16.0.1` | `172.16.0.254` | `172.16.0.255` |
| **VLAN 30** | HQ Operations | 120 | `/25` | `172.16.1.0` | `172.16.1.1` | `172.16.1.1` | `172.16.1.126` | `172.16.1.127` |
| **VLAN 20** | Finance & HR | 60 | `/26` | `172.16.1.128` | `172.16.1.129` | `172.16.1.129` | `172.16.1.190` | `172.16.1.191` |
| **VLAN 40** | Branch Ops| 50 | `/26` | `172.16.1.192` | `172.16.1.193` | `172.16.1.193` | `172.16.1.254` | `172.16.1.255` |
| **VLAN 10** | IT & Admin | 30 | `/27` | `172.16.2.0` | `172.16.2.1` | `172.16.2.1` | `172.16.2.30` | `172.16.2.31` |

#### WAN Subnetting (Point-to-Point Links)
**Private WAN Network Assigned:** `10.255.255.0/24` (Subnetted to `/30` for Router Links)

**Public WAN Network Assigned:** `203.0.113.0/30`

| Link Name | Connection | Hosts | Prefix | Network Address | First Usable | Last Usable | Broadcast Address |
| :--- | :--- | :---: | :---: | :--- | :--- | :--- | :--- |
| **Public WAN** | ISP to HQR2 | 2 | `/30` | `203.0.113.0` | `203.0.113.1` | `203.0.113.2` | `203.0.113.3` |
| **Private WAN 1**| HQR2 to BR1 | 2 | `/30` | `10.255.255.0` | `10.255.255.1` | `10.255.255.2` | `10.255.255.3` |
| **Private WAN 2**| HQR1 to BR1 | 2 | `/30` | `10.255.255.4` | `10.255.255.5` | `10.255.255.6` | `10.255.255.7` |
| **Private WAN 3**| HQR2 to HQR1 | 2 | `/30` | `10.255.255.8` | `10.255.255.9` | `10.255.255.10` | `10.255.255.11` |

### 2. Layer 2 Switching & LAN Technologies

#### VLANs & 802.1Q Trunking
* **Access Ports:** Configured on interfaces connecting directly to end devices (Hosts/PCs).
* **Trunk Ports:** Configured on switch-to-switch and switch-to-router connections to allow multiple VLAN traffic to pass through.

#### EtherChannel (Link Aggregation)
* Bundled multiple physical links between the Distribution Switches (**HQDS1** and **HQDS2**) into a logical interface using **LACP (802.3ad)** to increase bandwidth and provide link redundancy.

#### Inter-VLAN Routing (Router-on-a-Stick / ROAS)
Implemented ROAS to enable communication between different VLANs across the network:
* **HQ Routers (HQR1 & HQR2) Sub-interfaces:**
  * `g0/2.10` for VLAN 10
  * `g0/2.20` for VLAN 20
  * `g0/2.30` for VLAN 30
* **Branch Router (BR1) Sub-interfaces:**
  * `g0/1.40` for VLAN 40
  * `g0/1.50` for VLAN 50

#### Spanning Tree Protocol (RSTP) & Load Balancing
Enabled **Rapid Spanning Tree Protocol (RSTP)** across all Headquarter switches for faster network convergence. Implemented VLAN load balancing by distributing the Root Bridge roles:
* **Root Bridge Load Balancing:**
  * **VLAN 10:** HQDS2 acts as the Primary Root Bridge.
  * **VLAN 20 & 30:** HQDS1 acts as the Primary Root Bridge.
* **Options (PortFast & BPDU Guard):** Enabled globally on all access layer switches. This ensures that every non-trunk port (connected to hosts) transitions to the forwarding state immediately, while being strictly protected against accidental Layer 2 loops.

### 3. Layer 3 Routing & High Availability
#### Static Routing
* Configured a default static route (`0.0.0.0 0.0.0.0`) to forward all internet-bound traffic toward the ISP via the outside interface (`g0/1`).

#### Dynamic Routing (OSPF)
* **Single Area OSPF:** All routers (HQR1, HQR2, BR1) are configured in **OSPF Area 0** (Backbone Area) to exchange routing information.
* **Passive Interfaces:** Applied `passive-interface` on all LAN-facing ports (interfaces not connected to other routers) to prevent unnecessary OSPF updates from broadcasting into the user networks, thereby enhancing security and reducing bandwidth consumption.
* **Default Route Injection:** Configured `default-information originate` on HQR2 to dynamically advertise the static default route to all other routers within the OSPF domain.

#### First Hop Redundancy Protocol (HSRP) & Load Balancing
Configured HSRP on the Headquarter routers (HQR1 & HQR2) to provide highly available (redundant) default gateways for the end devices. 

* **Configured Virtual IPs (VIP):**
  * **VLAN 10:** `172.16.2.1`
  * **VLAN 20:** `172.16.1.129`
  * **VLAN 30:** `172.16.1.1`

* **HSRP Gateway Load Balancing:**
  To optimize router resource utilization and prevent traffic bottlenecks, the active gateway roles are distributed between the two routers:
  * **HQR1:** Acts as the **Standby** router for VLAN 10, and the **Active** router for VLANs 20 & 30.
  * **HQR2:** Acts as the **Active** router for VLAN 10, and the **Standby** router for VLANs 20 & 30.
 
### 4. Network Security & Access Control

#### Basic Device Security
* **Encrypted Authentication:** Enabled password protection on all routers and switches to secure privileged EXEC mode access using encrypted passwords. 
  *(Note for simulation testing: The password is set to `1234`)*

#### Access Control Lists (ACLs)
Configured **Standard ACLs** to control traffic flow and enforce department-level security policies:
1. **IT & Admin Protection:** Denied all other VLANs from accessing VLAN 10.
2. **Finance Privacy:** Restricted access to VLAN 20; only VLAN 10 (IT) is permitted to access it.
3. **Guest Restrictions:** Blocked VLAN 50 (Guests) from reaching corporate operational networks (VLAN 30 & VLAN 40).

> **Disclaimer (Standard ACLs Limitation):** Because Standard ACLs block returning ICMP replies, it can result in failed pings, so some tests may not work as intended.

#### Endpoint Protection (Port Security)
Implemented Layer 2 Port Security on Access Switches to prevent rogue devices from connecting to the corporate network:
* **Corporate VLANs:** Configured with **MAC Address Sticky** to dynamically learn and lock the approved device's MAC address to its specific port. The violation mode is set to `Shutdown`.
* **Guest Network Exception:** Port Security is intentionally **disabled on VLAN 50 (Guests)** to allow flexible and temporary connectivity for visitors without triggering port lockdowns.

### 5. Network Infrastructure Services

#### Dynamic Host Configuration Protocol (DHCP)
Configured DHCP servers on **HQR2** and **BR1** to automatically assign IP addresses to end devices. The first few usable IP addresses in each subnet are intentionally excluded from the DHCP pools to prevent IP conflicts with the Virtual IPs (HSRP) and router physical interfaces.

* **HQR2 DHCP Configuration (Headquarters):**
  * **Excluded Addresses:** `172.16.2.1 - 172.16.2.3` (VLAN 10), `172.16.1.129 - 172.16.1.131` (VLAN 20), `172.16.1.1 - 172.16.1.3` (VLAN 30)
  * **Pool VLAN 10:** Network `172.16.2.0/27`
  * **Pool VLAN 20:** Network `172.16.1.128/26`
  * **Pool VLAN 30:** Network `172.16.1.0/25`

* **BR1 DHCP Configuration (Branch):**
  * **Excluded Addresses:** `172.16.1.193` (VLAN 40 Gateway), `172.16.0.1` (VLAN 50 Gateway)
  * **Pool VLAN 40:** Network `172.16.1.192/26`
  * **Pool VLAN 50:** Network `172.16.0.0/24`

#### Network Address Translation (NAT Overload / PAT)
Configured Port Address Translation (PAT) on the edge router to allow internal hosts to access the internet securely while conserving public IPv4 addresses.
* **Translation Rule:** Translates the entire private LAN network (`172.16.0.0/16`, covering all VLANs) to the single Public IP address of **HQR2's** outside interface (`203.0.113.2`).
* **Mechanism:** Utilizes Layer 4 transport port numbers (Port Overloading) to multiplex, track, and manage multiple simultaneous sessions from various internal PCs through a single public IP.

##
What I learned
* [x] Network Devices
* [x] Basic Device Security( Username&Password Authentication )
* [x] Subnetting( VLSM )
* [x] VLANs( Trunking, ROAS ) 
* [x] STP with options( PortFast, BPDU ) and RSTP
* [x] EtherChannel
* [x] Static Routing
* [x] OSPF
* [x] FHRP( HSRP )
* [x] StandardACLs
* [x] DHCP
* [x] NAT( Static/Dynamic )
* [x] Port Security


### OSPF Configuration
```
Router> en
Router# conf t
Router(config)# router ospf 1
Router(config-router)# router-id (router name - preferred loop back IP)
Router(config-router)# network (ip address) (wild card mask) area 0
Router(config-router)# passive-interface default
Router(config-router)# no passive-interface (port to use) 
```
### FHRP( HSRP ) Configuration
```
! active router
R1> en
R1# conf t
R1(config)# int (default gateway port)
R1(config-if)# encapsulation dot1q (VLAN number) <- when VLAN
R1(config-if)# ip address (physical IP) (subnet mask)
R1(config-if)# standby 1 ip (VIRTUAL IP)
R1(config-if)# standby 1 priority (priority - default is 100)
R1(config-if)# standby 1 preempt
! standby router
R2> en
R2# conf t
R2(config)# int (default gateway port)
R2(config-if)# encapsulation 
R2(config-if)# ip address (physical IP) (subnet mask)
R2(config-if)# standby 1 ip (VIRTUAL IP)
R2(config-if)# standby 1 priority (priority - default is 100)
R2(config-if)# standby 1 preempt
```
### FHRP( HSRP ) Configuration
```
SwitchB> enable
SwitchB# configure terminal
SwitchB(config)# interface range (port range)
SwitchB(config-if-range)# channel-protocol lacp
SwitchB(config-if-range)# channel-group 2 mode active
SwitchB(config-if-range)# int port-channel 2
SwitchB(config-if)# sw mode trunk
SwitchB(config-if)# sw trunk allowed vlan (VLAN number)
```


## The process
### past-21/04/2026
* [x] Blueprint first draft
* [x] Blueprint second draft
* [x] Layout Physical Infrastructures( Switches, Routers, Host )
* [x] Create VLAN 10, 20, 30, 40, 50
* [x] Apply VLAN to certain switch ports
* [x] Apply Trunk port between switches
> About ~ 15% progress

What is the problem? - routing and segmentation progress takes time.. need to speed things up...

What's next? - apply EtherChannel to trunkport, and configure RSTP on routers then turn on PortFast and BPDU Gaurd, and apply port security to switches

### 22-23/04/2026
* [x] apply EtherChannel(LACP) between DS switches
* [x] configure rapid-STP to all HQ switches then apply port fast and bpdu guard to all access switches
* [x] apply ROAS to all routers
* [x] apply OSPF and implement passive-interface between routers
> About ~ 53% progress

What is the problem? - need to review OSPF, and speed things up real fast

What's next? - apply NAT, DHCP and port security to the project, clean up read me

### 24/04/2026
* [x] apply DHCP to HQR1 and BR1
* [x] apply NAT to HQR1
* [x] apply PortSecurity on VLAN10, 20, 30 and 40
* [x] implement standardACLs policy
* [x] implement authentication to all routers and switches 
> Progess - 100%

What is the problem? - documentation takes time, need to finish the documentation fast

What's next? - write up the documentation

## Running the projects

## Video
