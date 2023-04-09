# Junos Study Notes

## Modes
```markdown
- Operational
    - Cisco's enable
- Configuration or Edit
```

## Configuring Junos Interfaces

### Log Definitions
- IFD (Physical Interface Desigination)
- IFL (Logical Interface)
- IFF (Interface Family)

### Interface Types
- Managment: *me0, fxp0, vme*
- Internal: *fxp1, fxp2, bme*
- Network:
    - Ethernet: *fe-, ge-, xe-, et-*
    - Services: *pime, pind, sp-, gre*
- Loopback: Logical interface lo0
    - One unit per Routing Instance

### Setting an IPv4 address
```
set interfaces ge-0/0/0 unit 0 family inet address x.x.x.x/CIDR
```
Using a dot notation for the unit
```
set interfaces ge-0/0/0.0 family inet address x.x.x.x/CIDR
```
### Output of `show' Command
```
[edit interfaces ge-0/0/0]

unit 0 {
    family inet {
        address x.x.x.x/24;
    }
}
```

## User Config and Auth

### Basic config for local password authentication
```
[system login]
user wayne {
    class NOC; /* Class definition below */
    authentication {
        encrypted-password xxxxxxx;
    }
}
class NOC {
    permissions [clear network]; /* Enter JUNOS specfic context here */
    allow-commands "configure private";
    deny-commands "ping";
    allow-configuration "interfaces | firewalls";
}
```
## SNMP

### Sample config
```
[edit snmp]

description SomeRouter;
locaion The_Cloud;
contact "The Grinch";
community public {
    clients {
        10.10.0.0/24;
    }
}    
trap-group MY_TRAPS {
    version v2;
    catagories {
        chassis;
        link;
        routing;

    }
    targets {
        1.1.1.1;
    }
}

```
