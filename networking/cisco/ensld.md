# CISCO ENSLD Certification Notes
This is a repo of notes taken while working on ENSLD (300-420) certification


## BGP
Commands

Start a BGP process on tcp port 179: 
`router bgp {local-as}`

Configure a bgp neighbor:
`neighbor {IP} {remote-AS}`

Advertise a netork:
`network {Prefix} mask {Subnet mask}`

Show BGP ipv4 unicast peers:
`show bgp ipv4 unicast summary`

To configure a neighbor as a Route Reflector Client
 - Under the BGP process:
`neighbor {IP} route-reflector-client` 

### Route Reflectors
- Configured per address family

Rule #1: 
 - RR will only forward routes learned from non-clients to clients. 
 - It will not advirtise routes to non-client peers.

