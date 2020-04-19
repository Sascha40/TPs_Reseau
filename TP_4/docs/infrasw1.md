# Configuration du infra-sw 1

```
hostname infra-sw1

interface Ethernet0/0
 switchport trunk allowed vlan 30
 switchport trunk encapsulation dot1q
 switchport mode trunk

interface Ethernet0/1
 switchport access vlan 30
 switchport mode access

interface Ethernet0/2
 switchport access vlan 30
 switchport mode access
```