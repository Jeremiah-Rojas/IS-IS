# IS-IS

## Network Topology

<img width="845" height="523" alt="image" src="https://github.com/user-attachments/assets/c5b2b0ba-c7a4-4ecf-b5f0-c86e22b93d6c" />

<br>IS-IS (Intermediate System to Intermediate System) is a routing protocol that allows for dynamic routing changes in a topology when a link failure occurs. IS-IS is an interior gateway protocol and operates inside an autonomous system (AS); an AS is a collection of networks under the same administrative domain. This protocol calculates the best path based on the lowest “cost” which is the link speed; the faster the cable the lower the cost and the slower the cable the higher the cost.

<br>IS-IS is unique and should be discussed in little detail:
- Uses layer 2 information to transfer layer 3 data such as routing tables
- Uses “levels” to define the role of a router:
  - L1: Routing inside an area 
  - L2: Routing between areas
  - L1/L2: Routing both inside an area and between areas. Acts as a boundary router connecting Level 1 and Level 2
- Uses NET (Network Entity Title ) Addresses. Part of this address is called the System ID which is the router’s main IS-IS identifier
  - Example Address: 49.0001.0000.0000.0001.00
    - Area: 49.0001
    - System ID 0000.0000.0001
    - NSEL: 00
- Uses “Areas” which can be defined as a logical group where routers share L1 (level 1) routing databases; this is different from a subnet and VLAN.

### Devices Used:
- Cisco IOSv 15.7 router
- GNS3 Software
- Ubuntu Container (running on VMware machine)

## Configurations
| R1 | R2 | R3 | R4 |
|----|----|----|----|
| enable<br>conf t<br>hostname R1<br><br>interface Loopback0<br>ip address 1.1.1.1 255.255.255.255<br>ip router isis CORE<br><br>interface g0/0<br>ip address 10.12.12.1 255.255.255.0<br>ip router isis CORE<br>no shutdown<br><br>router isis CORE<br>net 49.0001.0000.0000.0001.00<br>is-type level-1<br>end<br>wr | enable<br>conf t<br>hostname R2<br><br>interface Loopback0<br>ip address 2.2.2.2 255.255.255.255<br>ip router isis CORE<br><br>interface g0/0<br>ip address 10.12.12.2 255.255.255.0<br>ip router isis CORE<br>no shutdown<br><br>interface g0/1<br>ip address 10.23.23.2 255.255.255.0<br>ip router isis CORE<br>no shutdown<br><br>interface g0/2<br>ip address 10.24.24.2 255.255.255.0<br>ip router isis CORE<br>no shutdown<br><br>router isis CORE<br>net 49.0001.0000.0000.0002.00<br>is-type level-1-2<br>end<br>wr | enable<br>conf t<br>hostname R3<br><br>interface Loopback0<br>ip address 3.3.3.3 255.255.255.255<br>ip router isis CORE<br><br>interface g0/0<br>ip address 10.23.23.3 255.255.255.0<br>ip router isis CORE<br>no shutdown<br><br>router isis CORE<br>net 49.0002.0000.0000.0003.00<br>is-type level-2-only<br>end<br>wr | enable<br>conf t<br>hostname R4<br><br>interface Loopback0<br>ip address 4.4.4.4 255.255.255.255<br>ip router isis CORE<br><br>interface g0/0<br>ip address 10.24.24.4 255.255.255.0<br>ip router isis CORE<br>no shutdown<br><br>router isis CORE<br>net 49.0001.0000.0000.0004.00<br>is-type level-1<br>end<br>wr |
