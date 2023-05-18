# CISCO ENSLD Certification Notes
This is a repo of notes taken while working on ENSLD (300-420) certification


## BGP

### Design Considerations

#### Load Balancing Outbound Connection - Single ISP
Use the `maximum-paths` command to set number of paths to prefix

#### Load Sharing Outbound Connections - Mulithomed ISPs
 - Cannot use max-path command.
 - Use prefix list or access list to split traffic
 - Manually configure the split of traffic

#### Manipulate Inbound Connections - Single ISP
 - Single ISP with dual connection
 - Use route-map to advirtise MED value
 - ISP will prefer the connection with the lowest MED

#### Manipulate Inbound Connections - Multihomed ISPs
 - Dual ISP
 - Use route-map to utilize as-path prepending
 - This adds extra ASN advirtised for the prefix

### Route Reflectors
- Configured per address family

#### Rule #1: 
 - RR will only forward routes learned from non-clients to clients. 
 - It will not advirtise routes to non-client peers.

#### Rule #2
 - RR will forward all NLRI recieved from clients to non-clients and clients
 - RR clients with recognize a loop and discard the route

 #### Rule #3
  - eBGP acts normal with RR clients

### BGP Decision Process

1) Weight
2) Local Preference
3) Originator
    - Network or redist. statement
    - Aggregate-add
    - inboud NLRI
4) AIGP
5) AS Path
    - Most commonly used to make route selection with defualt values from above
6) Origin Code
    - i = coming from IGP
    - e = externally learned NLRI
    - ? = incomplete, most likely resdist.
7) MED - Multi Exit Disriminator
8) eBGP vs iBGP
#### Remaining tiebrakers
9) Lowest IGP Metric
10) Oldest eBGP route
11) Lowest Neighbor Router ID
12) Min Cluster List Length
13) Lowest Neighbor IP addess

### Commands

Start a BGP process on tcp port 179:
<pre> 
router bgp {<i>local-as</i>}
</pre>

Configure a bgp neighbor:
<pre>
neighbor {<i>IP</i>} {<i>remote-AS</i>}
</pre>

Advertise a netork:
<pre>
network {<i>Prefix</i>} mask {<i>Subnet mask</i>}
</pre>

Show BGP ipv4 unicast peers:
<pre>
show bgp ipv4 unicast summary
</pre>

To configure a neighbor as a Route Reflector Client
 - Under the BGP process:
 <pre>
neighbor {<i>IP</i>} route-reflector-client
</pre>

To configure multiple paths to a prefix with a single ISP:
 - Under address-family:
 <pre>
 maximum-paths {<i>number of paths</i>}
 </pre>

## OSPF

To form a neighbor, the following must match
 - Area
 - Area Type
 - Hello and Dead timers
 - MTU

Neighbors must have a unique `router-id`, the selection process:
1) Configured ID with the `router-id` command.
2) Highest Loopback address
3) Highest IP configured on an interface

Ethernet Defualt timers:
- Hello = 10 sec
- Dead = 40 sec (4x Hello)

NBMA (Non Broadcast Muiltiaccess)
 - Hello = 30 sec
 - Dead = 120 sec (4x Hello)

###  DR and BDR

224.0.0.5 - Multicast address for all OSPF routers

Router IDs are only used if OSPF Priority has NOT been configured first. By defualt, OSPF 
has a Priority of 1. We should set the priority higher if we want a router to be DR/BDR.
When the priority is set to 0, a router will NOT become a DR/BDR.

DR handles flooding of route updates. Routers send updates to the DR and the DR floods those
out to all neighbors on the ethernet segmant.

All routers form a full adjecencey with the DR/BDR and a 2-way/DROTHER with all others.

### LSAs

Type 1 (Router LSA):
 - Every router sends this type to identify itself and attached segments
 - Sent to DR/BDR

Type 2 (Network LSA):
 - Sent out by DR/BDR on a broadcast to identiy itself and routers on the segment
 - Point to Point (P2P) will not send this type

Type 3 (Summary LSA):
 - Used for area summerization
 - Most used for traffic manuipulation

Type 4 (Summary ASB ):
- How we get the ASBR on a segment
- Used to help routers in other areas OUTSIDE of the originating area find the ASBR.
  Otherwise, they would just use the Type 1 LSA to find the ASBR in the area.

Type 5 (AS External Link):
 - What re the external routes

Type 7 ():
 - What are the external routes for the NSSA

### Areas 

Stub Area
 - No type 4,5,7 LSA
 - Gets a defualt route from ABR

Total Stub
 - No type 3,4,5,7 LSA
 - Get defualt route

Not So Stub Area
 - Stub area with route redistribution
 - No Type 4,5 LSA
 - Sends Type 7 into area, at ABR turns into Type 5

### Authentication

Using passive interfaces 
 - Allow interface to praticipate in OSPF proccess but does not send out Hello packets
 - Shows as DOWN in `sh ip ospf int br`

MD5 Password Authentication:
 - Configured on the interface

HMAC-SHA-512 Authentication:
 - Allows to use key-chains
 - Rotating password, set expire times

### Commands

router ospf {Process number}
network {IP Addess} {Wildcard mask} area {Area number}
passive-interface {Interface Number}

(HMAC-SHA authentication from configure mode)
key chain {name}
key {number}
cryptographic-algorithm {encryption type}
key-string {password}

-interface config: 
 - ip ospf # area #
 - ip ospf network {network type}
(MD5 authentication)
 - ip ospf message-digest {key number} md5 {password}
 - ip ospf authentication message-digest (Turns on MD5 authentication)
(HMAC-SHA Authentication)
 - ip ospf authentication key-chain {name}

show ip ospf int br
show ip ospf nei


## EIGRP

Distance-vector protocol by definition, but has some link-sate features.
 - Exams may call it Hybrid protocol
 - Administrative Distance of 90

2 forms Classic and Named
 - Classic:
  - meant for IPv4 Unicast
  - specify the ASN in the process
 - Named:
  - can specify neighbors by address family (v4 and v6)
  - Unicast, Multicast, or by vrf

While we use a network statement and wildcard mask for neighbors, EIGRP uses that to determine 
which interfaces to use in the process.

Uses RTP - Reliable transport protocol
 - uses a series of sequance and ack packets for updates

224.0.0.10 - EIGRP Multicast address for hello packets
 - Route updates use unicast

Query packet happens when a route or connection goes down. All eigrp routers will respond to a query.

Hello packet timer defualt 5 seconds - WAN or slow links 60 seconds
Dead interval (Hold Timer) defualt 15 seconds - WAN or slow links 180 seconds

Summerization occurs on an interface basis. Mostly done in the Distribution layer
 - Leak Map can be used for traffic engineering out multiple summerized routes

Passive interface will not form an adjacency but the route will be advirtised out

Authentication for named mode is done under the EIGRP proccess and `address-family` but specified per interface.
 - md5
 - hmac-sha-256

Authentication for classic is done on the interface
 - Only md5
 - Can use `eigrp upgrade-cli _{Named Mode Process}_ to switch to named mode

### Metric

Uses 5 components, called K values:
 - Bandwidth * _(Used by EIGRP DUAL)_
 - Delay * _(Used by EIGRP DUAL)_
 - Reliability (Usually set to 0)
 - Load (Usually set to 0)
 - MTU (Usually set to 0)

Feasiable Distance is the route with the lowest metric this becomes the successor.
 - AD plus local metric
Advirtised distance or Reported distance is what is sent by the neighbor

### Feasibility Condition

- Backup route is called feasiable succesor

- If the Advirtised Distance is lower then our Feasible Distance

### Autonomous Systems/Query Domain

Different then areas or levels in that you need to redistribute between AS in EIGRP.
 - These are external EIGRP routes and have an Administrative Distance of 170.

Queries are sent when links go down, they must be responded to by. This creates a scalablity issue.

AS are used to limit query domains within an EIGRP design.
 - Stub routers do not advirtise to other EIGRP neighbors
 - Leak Map, is a route map to advirtise routes behind a stub router (Old way)
 - Stub Sites, only in named EIGRP, will not advirtise routes between configured WAN interfaces

### Commands

## IS-IS

### Basics

Mostly used in SP networks, but active in enterprise networks due to DNA Center.
 - DNA Center deploys IS-IS as an underlay

Some commonalities to OSPF
 - Link state protocol
 - Send LSP (Link state protocol units)
 - Uses areas
 - Supports v4 and v6 addresses

ES = End system
IS = Intermediate system

### Levels

Level 0
 - ES to IS

Level 1
- Intra-area IS to IS

Level 2
- Interarea IS to IS

Level 3
- AS to AS _(Cisco does not support level 3)_

### IS-IS Areas

While a link state protocol there is no need for an area 0, but it is best practice to have
transit area to preform level 2 communications.

Form adjecencies over layer 2
 - Different Layer 2 address for the hello dependant level

When a router is configured for `is-type level-1` it will only form a neighborship:
 - Only with other level-1 routers
 - AND the area number must match
 - This is intra-area communication

When a router is configured for `is type level-1-2` it will for adjencencies with:
 - level-1 routers
 - level-2 routers
 - Sends defualt route into level-1 areas

### NET

 NET: Network Entity Title
  - How an IS-IS router identifies it self, what area, and traffic types
  - Configured under the IS-IS process
  - 6 16-bit sections
  - Eaxmple: 49.0001.0000.0000.0001.00
  1) AFI (Almost always 49)
  2) Area
  3-5) System ID - Unique Identifier, like a MAC addess or router ID
  6) 00 = identifying self. Any other number identifys traffic type

### Metric
All Cisco routers have a metric of 10 for interfaces involved in IS-IS

For large environments you can use a wide metric to increase the maximum metric.

### Design

Three key points:
- Secure
    - Uses HMAC MD5 for password auth, also in the runnning config as well
    - Authentication can happen a three different points
        - Set password on the interface, good for core of network or level 1 connections
        - Set password for the area
        - Set password on the domain (which is the AFI in the NET)
- Stable
- Scaleable

#### Flat Network Design

- Utilize Level 2 and a single area, although areas can differ
    - Allows for future scaleablity with adding level 1-2 and new areas.
    - Running level 1 would limit future scaleablity
- Keeps it to a single datebase and lower CPU
    - Running level 1-2 would require all routers to maintain two databases

#### 3-Tier Campus
 - Core runs Level 2
    - Acts as that transit area
    - If level 1-2 then would cause routing loops with defualt route issues
 - Distro run Level 1-2
    - Sends in the defualt routes to the level 1 areas
 - Access runs Level 1

#### Hybrid
 - Used in collapsed core environments
 - Run level 1-2 in core
    - Maintains two databases
    - larger level 1 databases
 - No real transit area
    - Harder to scale later

### Commands

To start the IS-IS routing process:
 <pre>
 router is-is
 </pre>

To set the `is-type`:
- under the is-is process
<pre>
is-type {<i>level-1, level-2-only, level-1-2</i>} 
</pre>

To set the `net`:
- under the is-is process
<pre>
net {<i>96 bit Identifier</i>} 
</pre>

To set `metric-style` to wide:
- under is-is process
<pre>
meteric-style wide
</pre>

To set interface for IS-IS:
- under interface configuration
<pre>
ip router isis
</pre>

To set a password on an interface:
- under interface configuration
<pre>
isis password {<i>Strong Password</i>}
</pre>

To set a password for an area:
- under is-is process
<pre>
area-password {<i>Strong Password</i>}
</pre>

To set a password for a domian:
- under is-is process
<pre>
domain-password {<i>Strong Password</i>}
</pre>

## Multicast

One to many in a group...

224.0.0.0/24 these packets will not route past the local link.
| Multicast Address | Description |
|---|---|
| 221.0.0.1 | All Hosts or all systems |
| 224.0.0.2 | All Multicast routers |
| 224.0.0.5 | All OSPF routers |
| 224.0.0.6 | All OSPF DRs |
| 224.0.0.10 | EIGRP routers |
| 224.0.0.13 | All PIM routers |
| 224.0.1.39 | RP announcement |
| 224.0.1.40 | RP Discovery |

Uses reservered IEEE 0100.5e00 for MAC layer. Lower 23 bits maps to Layer 3 IP muiltcast
address.

### IGMP - Internet Group Managment Protocol
 - Layer 2 protocol to handle multicast group membership
#### v1
 - Join request to join group
 - Query message to see if any hosts want stream
 - Dead interval caused long time to leave group
 - Prune messages to leave a group
 - Does not track hosts requesting group
#### v2
 - Added a leave group message, triggers query
 - Query sent out to see if hosts still want stream
#### v3
 - Added support for SSM (Source Specific Mcast)
#### IGMP Snooping
 - Standards based protocol
 - Used to track mcast requests by MAC address 

### RPF (Reverse Path Forwarding)
 - Loop prevention method
 - If it was unicast traffic, is this the route I would take
 - Works with IGP to prevent multicast loops
 - Relies on unicast routing table

### PIM
 - Mulitcast IGP with neighborships
 - How mutilcast traffic gets routed
 - (S,G) -> (Source, Group) -> (192.168.1.25, 231.1.1.1)
 - (\*,G) -> (All sources, Group) -> (\*, 231.1.1.1)
 
### Source Trees
 - AKA SPT (Shortest Path Tree)
 - PIM default sends out packets all mutilcast interfaces
 - Routers use the source address to find shortest path
 - Easy to deploy, but lots of traffic overhead
 - Best suited for densely populated receivers
 - PIM-DM (Pim dense Mode)

### Shared Trees
 - Best suited for sparsely poulated receivers
 - Uses a RP (Rendezvous Point) to centrialize information for that group
 - After source is identified traffic ends up using SPT and not through RP
 - PIM-SM (Pim sparse mode)

### RP
- Static assignment
    - All groups
    - Specific groups
- AutoRP (Cisco Specific)
    - All groups
    - Specific groups via ACL
    - RP Announcement
    - RP Mapping Node (Usually same device)
        - Helps with RP Discovery messages
- BSR (Boot Strap Router configuration), industry standard similar to AutoRP
    - Configure boot starp canidate
    - Discovery auto populates through

### Bidir-PIM
 - Bi-direction PIM, used for bi-drectional multicast
 - PIM-SM deployment
 - Keeps RP in the mix and doesn tnot use a SPT

### SSM
 - Source Specific multicast
 - PIM-SM deployment
 - No RP
 - Uses IGMP v3
 - Allows for load balanceing from multiple sources
 - Used in service provider and broadcast TV

### MSDP
 - Used to connect RP together across different multicast domains
 - Typcially used with MP-BGP

### Commands

Turning on multicast routing for the global routing table
 - `ip multicast-routing`
 - add `vrf` and specify the name to add to a specfific vrf

To define an RP
 - `ip pim rp-addresss {IP_address}`

Under interface config (can be a range as well)
 - `ip pim dense-mode`
 - `ip pim sparse-mode`

Show the Multicast routing table
 - `show ip mroute`
