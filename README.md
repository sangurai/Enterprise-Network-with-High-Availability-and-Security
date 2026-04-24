# Enterprise Network with High Availability and Security
A **practical** implementation of CCNA concepts to design a real-world enterprise network.
> For detailed study notes and foundational concepts applied in this project, please refer to my [CCNA Learning Repository](https://github.com/sangurai/CCNA).

## Technologies
* Cisco Packet Tracer
### Networking Skill Applied
### 1. Network foundation & IP Adressing
#### Subnetting (VLSM) and IP Address allocation convention
- **1st** usable address of every subnets is the Virtual Router IP for HSRP
- **Next IP** becomes physical IP on router when doing sub-interfaces
- **the rest** addresses is assigned to hosts
#### 1. LAN Subnetting
**Major Network Assigned:** `172.16.0.0/16`
| VLAN | Department | Hosts | Prefix | Network Address | Virtual IP (HSRP) | First Usable | Last Usable | Broadcast Address |
| :--- | :--- | :---: | :---: | :--- | :--- | :--- | :--- | :--- |
| **VLAN 50** | Guests | 200 | `/24` | `172.16.0.0` | `172.16.0.1` | `172.16.0.1` | `172.16.0.254` | `172.16.0.255` |
| **VLAN 30** | HQ Operations | 120 | `/25` | `172.16.1.0` | `172.16.1.1` | `172.16.1.1` | `172.16.1.126` | `172.16.1.127` |
| **VLAN 20** | Finance & HR | 60 | `/26` | `172.16.1.128` | `172.16.1.129` | `172.16.1.129` | `172.16.1.190` | `172.16.1.191` |
| **VLAN 40** | Branch Ops| 50 | `/26` | `172.16.1.192` | `172.16.1.193` | `172.16.1.193` | `172.16.1.254` | `172.16.1.255` |
| **VLAN 10** | IT & Admin | 30 | `/27` | `172.16.2.0` | `172.16.2.1` | `172.16.2.1` | `172.16.2.30` | `172.16.2.31` |

#### 2. WAN Subnetting
**Major Network Assigned(Private WAN):**``





## What I learned
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
