
# Ch4 Network Layer Data Plane


### Network Core
Packet-switching: hosts break application-layer messages into packets
- network forwards packets from one router to the next, across links on path from source to destination

A **router** forwards packets from one network to another


![[Pasted image 20250926023522.png]]

**Packet Forwarding**
**Packet Routing**

Data Plane: 
- local, per-router
- routes datagram from router input to router output
Control plane:
- network-wide
- determines how datagram is routed along end-end path from src to dest
- two approaches
	- traditional routing
		- components in every router
	- SDN
		- forwarding tables in routers


### Router architecture
![[Pasted image 20250926213116.png]]
Switching fabric
- transfer pkt from input link to correct output link
- switching rate: rate at which pkts transfer
- interconnect is the most useful e.g. mxn switch
- ![[Pasted image 20250926214358.png]]
![[Pasted image 20250926213140.png]]
![[Pasted image 20250926220751.png]]

#### Packet scheduling
(From buffer to link)
- FCFS
- Priority
- round robin
- weighted fair queuing (weighted round robin)

### Network layer functions
![[Pasted image 20250926221531.png]]


### IPv4 Datagram Format
![[Pasted image 20250926221545.png]]

### IPv6 Datagram ![[Pasted image 20250926224732.png]]
IPv6 datagrams are carried in IPv4 datagram in IPv4 routers (IPv4 tunnelling)

### Subnets
- device interfaces that can physically reach each other without passing through an intervening router


- IP address: 32-bit identifier associated with each host or router interface
	- a subnet 
	- host part
- interface: connection between host/router and physical link
- subnet: a list of interfaces that physically reach each other without router
- router’s typically have multiple interfaces
- host typically has one or two interfaces (e.g., wired Ethernet, wireless 802.11)
### DHCP
- Host dynamically gets its IP address within its network via DHCP
	- also address of first hop router
	- name and IP of DNS serve
	- network mask
- A connection only holds it address while connected
### Network IP Addr
- A network is allocated a portion of the provider ISP's space

### Hierarchical Addressing
![[Pasted image 20250927075535.png]]

### Network Address Translation
1. All devices in local network share 1 IPv4 addr
2. This IPv4 is NAT to the local network IP addr
3. Router must be able to replace ingoing and outgoing packets with corresponding external IPv4 and NAT IP addr

![[Pasted image 20250926224617.png]]



### Flow Tables
- determine how to forward within the network
### Middleboxes
Transforms, inspects, filters and manipulates traffic for purposes other than for packet forwarding
e.g.
- firewalls
- NAT
- load balancers
- deep packet inspection



# Ch5 Network Layer Control Plane
How to route packets from one network to another?



Routing Algorithms
- **Global**: (all routers have complete info)
	- **Link State** algos 
	- Dijkstra
	- However prone to oscillation when there are changes while routing
	- Router may advertise outdated cost
	- Router computes its own table
- **Decentralised**: (only know link costs to neighbours)
	- **Distance Vector** algos 
	- Bellman Ford
	- May have routing loops
	- Propagate own error through network
- Dynamic/static (how fast routes change based on link cost changes)

### Intra-ISP: Open Shortest Path First (OSPF) routing
- each router floods OSPF link-state advertisements (directly over IP rather than using TCP/UDP) to all other routers in entire AS
- multiple link costs metrics possible: bandwidth, delay
- each router has full topology, uses Dijkstra’s algorithm to compute forwarding table
- intra-ISP, so smaller range, can use full topo
- two level hierarchy (local area, backbone)
![[Pasted image 20250927091015.png]]

### Inter-ISP: Border Gateway Protocol (BGP) routing
- advertise to neighbouring networks its reachability

### SDN Control Plane
![[Pasted image 20250927121449.png]]
![[Pasted image 20250927121606.png]]
### Internet Control Message Protocol (ICMP)
- used by hosts and routers to communicate network-level information
	- error reporting: unreachable host, network, port, protocol
	- echo request/reply (used by ping)
- network-layer “above” IP:
	- ICMP messages carried in IP datagrams
- ICMP message: type, code plus first 8 bytes of IP datagram causing error
