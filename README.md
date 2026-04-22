# Enterprise Network with High Availability and Security
A **practical** implementation of CCNA concepts to design a real-world enterprise network.
> For detailed study notes and foundational concepts applied in this project, please refer to my [CCNA Learning Repository](https://github.com/sangurai/CCNA).

## Technologies
* Cisco Packet Tracer
### Networking Skill Applied
* [x] Network Devices
* [ ] Baic Device Security( Username&Password Authentication )
* [x] Subnetting( VLSM )
* [x] VLANs( Trunking, ROAS ) 
* [x] STP with options( PortFast, BPDU ) and RSTP
* [x] EtherChannel
* [ ] Static Routing
* [x] OSPF
* [x] FHRP( HSRP )
* [ ] StandardACLs
* [ ] DHCP
* [ ] NAT( Static/Dynamic )
* [ ] Port Security

## What I learned
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

## Running the projects

## Video
