# Configuration du client-sw 2

```
hostname client-sw2

interface Ethernet0/0
 switchport trunk allowed vlan 10,20
 switchport trunk encapsulation dot1q
 switchport mode trunk

interface Ethernet0/1
 switchport trunk allowed vlan 10,20
 switchport trunk encapsulation dot1q
 switchport mode trunk

interface Ethernet0/2
 switchport access vlan 10
 switchport mode access

interface Ethernet0/3
 switchport access vlan 20
 switchport mode access
```

Petit `show vlan` pour les afficher les vlans déclarés: 

```
client-sw2#show vlan

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Et1/0, Et1/1, Et1/2, Et1/3
                                                Et2/0, Et2/1, Et2/2, Et2/3
                                                Et3/0, Et3/1, Et3/2, Et3/3
10   admins                           active    Et0/2
20   guests                           active    Et0/3
30   infras                           active    
```