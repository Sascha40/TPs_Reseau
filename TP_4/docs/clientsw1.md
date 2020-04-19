# Configuration du client-sw 1

```
hostname client-sw1

interface Ethernet0/0
 switchport trunk allowed vlan 10,20
 switchport trunk encapsulation dot1q
 switchport mode trunk

interface Ethernet0/1
 switchport access vlan 10
 switchport mode access

interface Ethernet0/2
 switchport access vlan 20
 switchport mode access

interface Ethernet0/3
 switchport trunk allowed vlan 10,20
 switchport trunk encapsulation dot1q
 switchport mode trunk
```

Petit `show vlan` pour les afficher les vlans déclarés: 

```
client-sw1#show vlan

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Et1/0, Et1/1, Et1/2, Et1/3
                                                Et2/0, Et2/1, Et2/2, Et2/3
                                                Et3/0, Et3/1, Et3/2, Et3/3
10   admins                           active    Et0/1
20   guests                           active    Et0/2
30   infras                           active    
```