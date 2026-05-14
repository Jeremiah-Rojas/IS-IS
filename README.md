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

__Note: Routers R1, R2, and R4 are on level 1, but R3 is on level 2. R2 is the only router in this topology that is currently on both level 1 and level 2 which is normal.__

<br>Understanding the commands:
- ip router isis CORE: This enables IS-IS on the interface under the process named “CORE”; You could change this process name to anything
- router isis CORE: This enters the configuration mode for “CORE”
- net 49.0001.0000.0000.0001.00: Sets the router’s NET address
- is-type level-1: Configures the router to only operate in level 1. 


<br>The images below show IS-IS fully functional.


<br>
Router 1 details:
<br><img width="836" height="327" alt="image" src="https://github.com/user-attachments/assets/31606e32-ba62-4d52-b00c-0d864f93e65b" />

Output of “show ip route isis”:
<img width="836" height="234" alt="image" src="https://github.com/user-attachments/assets/34283c74-b742-4f3c-861f-587aac792bc9" />


<br>
Router 2 details:
<br><img width="848" height="480" alt="image" src="https://github.com/user-attachments/assets/bfe4c5ee-da42-4308-b716-d23b66103821" />

Output of “show ip route isis”:
<img width="843" height="202" alt="image" src="https://github.com/user-attachments/assets/5b8d7cb4-aef8-4776-ba41-2c8ed4a3fb2d" />


<br>
Router 3 details:
<br><img width="850" height="287" alt="image" src="https://github.com/user-attachments/assets/76d8f7f2-a826-4b44-aa6c-e70f8cec90aa" />

Output of “show ip route isis”:
<img width="852" height="262" alt="image" src="https://github.com/user-attachments/assets/0e331c16-805c-4376-b61c-360711e9afbc" />


<br>
Router 4 details:
<br><img width="854" height="334" alt="image" src="https://github.com/user-attachments/assets/8faacdcb-d68a-4db6-aaf2-b6655c8a6944" />

Output of “show ip route isis”:
<img width="859" height="243" alt="image" src="https://github.com/user-attachments/assets/f1c8ae6e-d743-4af6-aa23-ed01e6ddc693" />

<br><br>
Understanding the output:
- The “Holdtime” column shows the amount of seconds the link will stay up until it receives another IS-IS “hello” message; which is just a packet saying the neighbor is still active.
- The “Circuit Id” column is a unique identifier for that link
<br>
- lSPID: identifier the route and fragment of the LSP. The LSP is a packet that contains a router’s topology information.
- LSP Seq Num: Sequence number of the LSP which is used to determine the newest topology data
- LSP Checksum: verifies that the LSP has not been corrupted. If it changes, routers treat the LSP as if it had been updated
- LSP Holdtime / Rcvd : Holdtime is the remaining time the link will remain active if it is not refreshed. Rcvd is the time since the LSP has been last updated
- ATT / P / OL: ATT is equal to 1 when a router can reach level 2 or else it is set to 0. P indicates a connection is broken within an area. OL is set to 1 when the router should not be used for forwarding but is set to 0 otherwise.

## Conclusion

