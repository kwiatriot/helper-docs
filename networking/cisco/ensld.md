# CISCO ENSLD Certification Notes
This is a repo of notes taken while working on ENSLD (300-420) certification


## BGP
Commands

Start a BGP process on tcp port 179: 
`router bgp {local-as}`


Configure a bgp neighbor
`neighbor {IP} {remote-AS}`

Advertise a netork
`network {Prefix} mask {Subnet mask}`