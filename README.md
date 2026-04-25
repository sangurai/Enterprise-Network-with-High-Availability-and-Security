# Enterprise Network with High Availability and Security
A **practical** implementation of CCNA concepts to design a real-world enterprise network.
> For detailed study notes and foundational concepts applied in this project, please refer to my [CCNA Learning Repository](https://github.com/sangurai/CCNA).

![Project Image](https://github.com/sangurai/Enterprise-Network-with-High-Availability-and-Security/blob/dc0a4d8ad6be78a768a4d39767bc7c5744d6a134/readmeImage/Network%20Diagram.png)


## Video Demonstration


https://github.com/user-attachments/assets/48ba0351-e6a4-42d5-9f5c-4d79952a370f


https://github.com/user-attachments/assets/c4574bf1-1f30-4496-bf23-76421770dd2f


https://github.com/user-attachments/assets/165bb2c4-26fc-4c0b-9b33-039cbfec0c6c


https://github.com/user-attachments/assets/c9d06b23-dfbd-4a8d-85f0-c59451d71fb3


https://github.com/user-attachments/assets/7da73b4d-7b37-4d6e-9e81-2ece430b89b0


https://github.com/user-attachments/assets/0020c9ed-d9d6-4499-a76a-cd5224f5019b


## Technologies
* Cisco Packet Tracer


## Networking Skills Applied

### 1. Network Foundation & IP Addressing
#### Subnetting (VLSM) & IP Address Allocation Convention
The IP allocation follows these conventions:
* **1st Usable IP:** Assigned as the **Virtual Router IP** for HSRP.
* **Next Usable IPs:** Assigned as the **Physical IPs** on the router's sub-interfaces.
* **Remaining IPs:** Allocated to the end devices (hosts) within the subnet.

#### LAN Subnetting
> *For detailed subnetting calculations, please refer to my [handwrittenDraft.pdf](https://github.com/sangurai/Enterprise-Network-with-High-Availability-and-Security/blob/dc0a4d8ad6be78a768a4d39767bc7c5744d6a134/Blueprint/handwrittenDraft.pdf)*

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
![Trunking HeadQuarter](https://github.com/sangurai/Enterprise-Network-with-High-Availability-and-Security/blob/3de42b111f69f7261f6e0d589050b178ef1dcb11/readmeImage/full%20trunking.png)

![Trunking Branch](https://github.com/sangurai/Enterprise-Network-with-High-Availability-and-Security/blob/3de42b111f69f7261f6e0d589050b178ef1dcb11/readmeImage/trunkingBranch.png)

* **Access Ports:** Configured on interfaces connecting directly to end devices (Hosts/PCs).
* **Trunk Ports:** Configured on switch-to-switch and switch-to-router connections.



#### EtherChannel (Link Aggregation)
![readmeImage/trunking.png](https://github.com/sangurai/Enterprise-Network-with-High-Availability-and-Security/blob/3de42b111f69f7261f6e0d589050b178ef1dcb11/readmeImage/trunking.png)

* Bundled multiple physical links between the Distribution Switches (**HQDS1** and **HQDS2**) into a logical interface using **LACP (802.3ad)**.


#### Inter-VLAN Routing (Router-on-a-Stick / ROAS)
![ROAS](https://github.com/sangurai/Enterprise-Network-with-High-Availability-and-Security/blob/3de42b111f69f7261f6e0d589050b178ef1dcb11/readmeImage/router%20on%20a%20stick.png)
![ROASBranch](https://github.com/sangurai/Enterprise-Network-with-High-Availability-and-Security/blob/3de42b111f69f7261f6e0d589050b178ef1dcb11/readmeImage/router%20on%20a%20stick%20branch.png)

Implemented ROAS:
* **HQ Routers (HQR1 & HQR2) Sub-interfaces:**
  * `g0/2.10` for VLAN 10
  * `g0/2.20` for VLAN 20
  * `g0/2.30` for VLAN 30
* **Branch Router (BR1) Sub-interfaces:**
  * `g0/1.40` for VLAN 40
  * `g0/1.50` for VLAN 50

#### Spanning Tree Protocol (RSTP) & Load Balancing
Enabled **Rapid Spanning Tree Protocol (RSTP)** across all Headquarter switches. Implemented VLAN load balancing by distributing the Root Bridge roles:
* **Root Bridge Load Balancing:**

  ![VLAN10](https://github.com/sangurai/Enterprise-Network-with-High-Availability-and-Security/blob/3de42b111f69f7261f6e0d589050b178ef1dcb11/readmeImage/RSTP%20VLAN10.png)
  
  * **VLAN 10:** HQDS2 acts as the Primary Root Bridge.
 
  ![https://github.com/sangurai/Enterprise-Network-with-High-Availability-and-Security/blob/3de42b111f69f7261f6e0d589050b178ef1dcb11/readmeImage/RSTP%20VLAN10.png](https://github.com/sangurai/Enterprise-Network-with-High-Availability-and-Security/blob/3de42b111f69f7261f6e0d589050b178ef1dcb11/readmeImage/RSTP%20VLAN20%2030.png)
  * **VLAN 20 & 30:** HQDS1 acts as the Primary Root Bridge.
* **PortFast & BPDU Guard:** Enabled globally on all access layer switches.

### 3. Layer 3 Routing & High Availability
#### Static Routing
* Configured a default static route (`0.0.0.0 0.0.0.0`) to forward network traffic toward the ISP via the outside interface (`g0/1`).

#### Dynamic Routing (OSPF)
![OSPF](https://github.com/sangurai/Enterprise-Network-with-High-Availability-and-Security/blob/3de42b111f69f7261f6e0d589050b178ef1dcb11/readmeImage/Area0.png)

* **Single Area OSPF:** All routers (HQR1, HQR2, BR1) are configured in **OSPF Area 0** (Backbone Area).
* **Passive Interfaces:** Applied `passive-interface` on all LAN-facing ports (interfaces not connected to other routers) .
* **Default Route Injection:** Configured `default-information originate` on HQR2.

#### First Hop Redundancy Protocol (HSRP) & Load Balancing
Configured HSRP on the Headquarter routers (HQR1 & HQR2). 
![HSRP](https://github.com/sangurai/Enterprise-Network-with-High-Availability-and-Security/blob/3de42b111f69f7261f6e0d589050b178ef1dcb11/readmeImage/HSRP.png)

* **Configured Virtual IPs (VIP):**
  * **VLAN 10:** `172.16.2.1`
  * **VLAN 20:** `172.16.1.129`
  * **VLAN 30:** `172.16.1.1`

* **HSRP Gateway Load Balancing:**
  The active gateway roles are distributed between the two routers:
  * **HQR1:** Acts as the **Standby** router for VLAN 10, and the **Active** router for VLANs 20 & 30.
  * **HQR2:** Acts as the **Active** router for VLAN 10, and the **Standby** router for VLANs 20 & 30.
 
### 4. Network Security & Access Control

#### Basic Device Security
* **Encrypted Authentication:** Enabled password protection on all routers and switches to secure privileged EXEC mode and users level access using encrypted passwords. 
  *(Note for simulation testing: The password is set to `1234`)*

#### Access Control Lists (ACLs)
Configured **Standard ACLs** and enforce department-level security policies:
1. **IT & Admin Protection:** Denied all other VLANs from accessing VLAN 10.
2. **Finance Privacy:** Restricted access to VLAN 20; only VLAN 10 (IT) is permitted to access it.
3. **Guest Restrictions:** Blocked VLAN 50 (Guests) from reaching corporate operational networks (VLAN 30 & VLAN 40).

> **Disclaimer (Standard ACLs Limitation):** Because Standard ACLs block returning ICMP replies, it can result in failed pings, so some tests may not work as intended.

#### Endpoint Protection (Port Security)
Implemented Layer 2 Port Security on Access Switches at the corporate network:
* **Corporate VLANs:** Configured with **MAC Address Sticky** to lock the approved device's MAC address to its specific port. The violation mode is set to `Shutdown`.
* **Guest Network Exception:** Port Security is intentionally **disabled on VLAN 50 (Guests)** .

### 5. Network Infrastructure Services

#### Dynamic Host Configuration Protocol (DHCP)
Configured DHCP servers on **HQR2** and **BR1**. The first few usable IP addresses in each subnet are intentionally excluded from the DHCP pools.

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
Configured Port Address Translation (PAT) on the edge router.
* **Translation Rule:** Translates the entire private LAN network (`172.16.0.0/16`, covering all VLANs) to the single Public IP address of **HQR2's** outside interface (`203.0.113.2`).


## Developer Log
**High-Level Workflow:**
`Requirement` ➔ `Physical Infrastructure` ➔ `Layer 2 Config` ➔ `Layer 3 Config` ➔ `Infrastructure Services` ➔ `Network Security` ➔ `Test` ➔ `Documentation`
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

### 25/04/2026
* [x] write up the documentation and upload test video
> Progess - 100%

What is the problem? - some of the port is not configured

What's next? - clean up the project, write a clean resume and move to the next exciting project!

## Running the Project

### Prerequisites
* [**Cisco Packet Tracer**](https://www.netacad.com/resources/lab-downloads?courseLang=en-US):Version 9.0

### How to Open & Initialize
1. **Download** the repository to your local machine.
2. Double-click the `.pkt` file to open it in Cisco Packet Tracer.
3. **Wait** for Network Convergence and enjoy!

## Feature Verification & Test Cases

Here is a straightforward testing guide to verify implemented protocols:

| Feature | Test Action | Expected Result |
| :--- | :--- | :--- |
| **BPDU Guard** | Connect another switch (or any device generating BPDUs) to an access port on the HQ switches. | The port immediately transitions to the `err-disable` (shutdown) state, turning the link indicator red to strictly prevent Layer 2 loops. |
| **PortFast** | Unplug a PC and plug it back into an access port. | The port transitions directly to the forwarding state (green indicator) immediately, bypassing the standard STP listening and learning phases (no 30-second delay). |
| **Port Security** | Disconnect a registered PC from its switch port and connect a "Rogue PC" (with a different MAC address). | The switch port detects the MAC violation and changes its state to `shutdown` (red indicator) instantly. |
| **DHCP** | Open a PC in any VLAN, navigate to IP Configuration, and toggle the setting from Static to DHCP. | The PC successfully obtains an IP address, Subnet Mask, and Default Gateway matching its specific VLAN pool. |
| **FHRP (HSRP)** | Start a continuous ping (`ping -t 8.8.8.8`) from an HQ PC to the outside internet, then delete the cable connected to the Active HSRP router. | The ping drops briefly (1-2 timeouts) but automatically resumes as the Standby router seamlessly takes over the Virtual IP gateway role. |
| **ACLs** | From a PC in the Guest Network (VLAN 50), attempt to ping a device in the HQ Operations network (VLAN 30). | The ping fails with a `Destination host unreachable` or `Request timed out` message, confirming the traffic is actively filtered and blocked. |

