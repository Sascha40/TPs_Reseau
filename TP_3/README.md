# TP3 Réseau

## I.Router-on-a-stick

PC1 : IP: `10.3.10.1/24` VLAN: `10` GATEWAY: `10.3.10.254`     
PC2 : IP: `10.3.20.2/24` VLAN: `20` GATEWAY: `10.3.20.254`  
PC3 : IP: `10.3.20.3/24` VLAN: `20` GATEWAY: `10.3.20.254`  
PC4 : IP: `10.3.30.4/24` VLAN: `30` GATEWAY: `10.3.30.254`  
P1  : IP: `10.3.40.1/24` VLAN: `40` GATEWAY: `10.3.40.254`  

Le routeur a uen IP dans chaque réseau, il s'agit de la gateway des différentes machines.

Premièrement pour mettre en place du routage inter-VLANs on va tout simplement commencer par configurer nos VLANs.

* Création du VLAN10(à faire pour chaque VLAN) :  
    ```
        IOU1#conf t
        IOU1(config)#vlan 10
        IOU1(config)#name vlan_10
        IOU1(config)#exit
    ```
* Création du port trunk (à faire sur les ports entre les deux switchs mais aussi sur celui entre SW1 et R1) :
    ```
    IOU1(config)#interface e0/0
    IOU1(config)#switchport trunk encapsulation dot1q
    IOU1(config-if)#switchport mode trunk 
    IOU1(config-if)#switchport trunk allowed vlan 20,30,40
    IOU1(config-if)#no shutdown
    IOU1(config-if)#exit
    ```   

* A partir de là, seulement notre PC2 et PC3 peuvent se ping car ils sont dans le même réseau :   
    ```  
        PC2> ping 10.3.20.3
        84 bytes from 10.3.20.3 icmp_seq=1 ttl=64 time=0.571 ms
        84 bytes from 10.3.20.3 icmp_seq=2 ttl=64 time=0.642 ms
        84 bytes from 10.3.20.3 icmp_seq=3 ttl=64 time=0.633 ms
    ```
* Il faut maintenant configurer le routeur pour qu'il puisse jouer son rôle. Pour cela on va lui créer ses 4 sous-interfaces :

    ``` 
    R1(config)#interface e0/0.10
    R1(config-subif)#encapsulation dot1Q 10 
    R1(config-subif)#ip address 10.3.10.254 255.255.255.0 
    R1(config-subif)#no shutdown
    R1(config-subif)#exit
    ```
Ensuite il faut trunker le port qui relie le SW1 au routeur et maintenant une machine d'un réseau X peut ping une machine d'un réseau Y.

Exemple avec le PC2 qui ping le PC4 : 
``` 
    PC2> ping 10.3.30.4
    84 bytes from 10.3.30.4 icmp_seq=1 ttl=64 time=0.601 ms
    84 bytes from 10.3.30.4 icmp_seq=2 ttl=64 time=0.667 ms
    84 bytes from 10.3.30.4 icmp_seq=3 ttl=64 time=0.673 ms
    84 bytes from 10.3.30.4 icmp_seq=3 ttl=64 time=0.622 ms
```
