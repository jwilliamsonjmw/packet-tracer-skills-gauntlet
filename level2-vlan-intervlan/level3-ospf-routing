# Level 3 – OSPF Dynamic Routing
### Cisco Packet Tracer Skills Gauntlet

![Topology](level3-topology.png)

## Project Overview
Built a multi-router network using OSPF (Open Shortest Path First) dynamic routing. Configured three Cisco 1941 routers with point-to-point links, enabled OSPF Area 0 on all routers, and verified automatic route advertisement across the entire network. End-to-end connectivity confirmed with a ping from PC1 (192.168.1.x) to PC2 (192.168.2.x) traversing three routers — with zero static routes configured.

---

## Network Topology

```
[PC1]──[SW1]──[R1]──────[R2]──────[R3]──[SW2]──[PC2]
        LAN1        R1↔R2     R2↔R3        LAN2
   192.168.1.0/24  10.0.12.0/30  10.0.23.0/30  192.168.2.0/24
```

---

## IP Addressing

| Device | Interface | IP Address | Subnet Mask |
|--------|-----------|-----------|-------------|
| PC1 | Fa0 | 192.168.1.10 | 255.255.255.0 |
| Router1 | Gig0/0 | 192.168.1.1 | 255.255.255.0 |
| Router1 | Gig0/1 | 10.0.12.1 | 255.255.255.252 |
| Router2 | Gig0/0 | 10.0.12.2 | 255.255.255.252 |
| Router2 | Gig0/1 | 10.0.23.1 | 255.255.255.252 |
| Router3 | Gig0/0 | 10.0.23.2 | 255.255.255.252 |
| Router3 | Gig0/1 | 192.168.2.1 | 255.255.255.0 |
| PC2 | Fa0 | 192.168.2.10 | 255.255.255.0 |

---

## Router Configuration

### Router1
```cisco
interface GigabitEthernet0/0
 ip address 192.168.1.1 255.255.255.0
 no shutdown
interface GigabitEthernet0/1
 ip address 10.0.12.1 255.255.255.252
 no shutdown
router ospf 1
 network 192.168.1.0 0.0.0.255 area 0
 network 10.0.12.0 0.0.0.3 area 0
```

### Router2
```cisco
interface GigabitEthernet0/0
 ip address 10.0.12.2 255.255.255.252
 no shutdown
interface GigabitEthernet0/1
 ip address 10.0.23.1 255.255.255.252
 no shutdown
router ospf 1
 network 10.0.12.0 0.0.0.3 area 0
 network 10.0.23.0 0.0.0.3 area 0
```

### Router3
```cisco
interface GigabitEthernet0/0
 ip address 10.0.23.2 255.255.255.252
 no shutdown
interface GigabitEthernet0/1
 ip address 192.168.2.1 255.255.255.0
 no shutdown
router ospf 1
 network 10.0.23.0 0.0.0.3 area 0
 network 192.168.2.0 0.0.0.255 area 0
```

---

## Verification

### OSPF neighbor adjacency (Router2 CLI)
```
%OSPF-5-ADJCHG: Process 1, Nbr 192.168.1.1 on GigabitEthernet0/0
from LOADING to FULL, Loading Done
```

### Router1 routing table (`show ip route`)
```
C    10.0.12.0/30 is directly connected, GigabitEthernet0/1
O    10.0.23.0/30 [110/2] via 10.0.12.2
C    192.168.1.0/24 is directly connected, GigabitEthernet0/0
O    192.168.2.0/24 [110/3] via 10.0.12.2
```
`O` = OSPF learned route. Router1 automatically knows about 192.168.2.0/24 without any static route configured.

### Ping result (PC1 → PC2)
```
ping 192.168.2.10 → Sent=4, Received=3, Lost=1 (25% loss)
```
25% loss is expected — first packet triggers ARP resolution across new subnets. TTL=125 confirms traffic traversed 3 router hops (128 - 3 = 125).

---

## Concepts Learned

**What is OSPF?**
Open Shortest Path First is a link-state dynamic routing protocol. Routers running OSPF automatically discover neighbors, exchange link-state information, and calculate the best path to every known network using Dijkstra's algorithm. No manual static routes needed — the network self-configures.

**Why /30 for router-to-router links?**
A /30 subnet provides exactly 2 usable IP addresses — one for each end of a point-to-point link. Using a /24 would waste 252 addresses on a link with only 2 devices. Efficient subnetting is standard practice for WAN and transit links.

**OSPF neighbor adjacency**
Routers send OSPF hello packets out their interfaces. When two routers hear each other's hellos, they form a neighbor relationship and exchange their link-state databases. FULL state means complete adjacency — both routers have identical databases and know every route in the area.

**OSPF Area 0**
The backbone area that all OSPF routers must belong to (or connect to). All three routers in this lab are in Area 0, meaning they share a single link-state database and have full visibility of every network.

**Reading the route table**
`C` = directly connected, `L` = local router IP, `O` = OSPF learned, `S` = static. The `[110/3]` notation means administrative distance 110 (OSPF default) and cost 3. Lower cost = preferred path.

**TTL (Time to Live)**
Every IP packet starts with a TTL value (128 on Windows). Each router hop decrements TTL by 1. A TTL of 125 on the ping reply confirms the packet traversed exactly 3 routers (128 - 3 = 125). TTL also prevents packets from looping forever on misconfigured networks.

**`show ip route`**
The most important verification command for routing. Shows every network the router knows about, how it learned it (C/L/O/S), and via which next-hop address. Always run this after configuring OSPF to confirm routes are being advertised correctly.

**OSPF `network` command wildcard mask**
The wildcard mask is the inverse of the subnet mask. `0.0.0.255` matches any IP in a /24. `0.0.0.3` matches exactly a /30. This tells OSPF which interfaces to include in the routing process.

---

## Screenshots

| File | Description |
|------|-------------|
| `level3-topology.png` | Full topology with all green links |
| `level3-router1-config.png` | Router1 interface configuration |
| `level3-router2-config.png` | Router2 interface configuration |
| `level3-router3-config.png` | Router3 interface configuration |
| `level3-router1-ospf.png` | Router1 OSPF configuration |
| `level3-router2-ospf.png` | Router2 OSPF adjacency message |
| `level3-router3-ospf.png` | Router3 OSPF adjacency message |
| `level3-show-ip-route.png` | Router1 routing table with OSPF routes |
| `pc1-ipconfig.png` | PC1 IP configuration |
| `pc2-ipconfig.png` | PC2 IP configuration |
| `level3-ping-success.png` | Successful ping PC1 → PC2 across 3 routers |

---

## Tools Used
- Cisco Packet Tracer 8.x
- Cisco IOS CLI
- Cisco 1941 Routers (x3)
- Cisco 2960 Switches (x2)

## Project File
Download the Packet Tracer file: [level3-ospf-routing.pkt](level3-ospf-routing.pkt)

---

*Part of the Packet Tracer Skills Gauntlet — a progressive 5-level networking project series.*
*Previous: Level 2 — VLANs + Inter-VLAN Routing*
*Next: Level 4 — ACLs + Network Security*
