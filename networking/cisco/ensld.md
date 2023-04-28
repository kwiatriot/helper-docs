# CISCO ENSLD Certification Notes
This is a repo of notes taken while working on ENSLD (300-420) certification


## BGP

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


## IS-IS

### Commands

To start the IS-IS routing process:
 <pre>
 router is-is
 </pre>

To set the `is-type`:
- under the is-is process
<pre>
is-type {<i>level-1, level-2, level-1-2</i>} 
</pre>

Mostly used in SP networks, but active in enterprise networks due to DNA Center.
 - DNA Center deploys IS-IS as an underlay

Some commonalities to OSPF

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

When a router is configured for `is-type level-1` it will only form a neighborship:
 - Only with other level-1 routers
 - AND the area number must match
 - This is intra-area communication

When a router is configured for `is type level-1-2` it will for adjencencies with:
 - level-1 routers
 - level-2 routers
 - Sends defualt route into level-1 areas