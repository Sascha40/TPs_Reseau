# TP2

## I. Simplest setup

* Mettre en place la topologie ci-dessus :  
    
    On va dans un premier temps définir l'IP de nos deux PCs en tapant la commande `ip 10.2.1.1(ou 2)/24`

* Faire communiquer les deux PCs :   
  
    * Avec un ping qui fonctionne :    
    ```      
    PC2> ping 10.2.1.1
    84 bytes from 10.2.1.1 icmp_seq=1 ttl=64 time=0.173 ms
    84 bytes from 10.2.1.1 icmp_seq=2 ttl=64 time=0.654 ms
    84 bytes from 10.2.1.1 icmp_seq=3 ttl=64 time=0.663 ms
    ```       
    
    *  Déterminer le protocole utilisé par ping à l'aide de Wireshark :     
        
        Le protocole ICMP est le protocol utilisé par `ping`.

    * Analyser les échanges ARP :   
        * Utiliser Wireshark et mettre en évidence l'échange ARP entre les deux machines (ARP Request et ARP Reply) :   
            
            Sur Wireshark on peut voir que dans un premier temps PC2 balance sur sa broadcast "a qui est cette IP", ensuite le PC1 va répondre et le PC2 va savoir à quelle addresse MAC appartient l'ip `10.2.1.1`. Dans notre cas l'addresse MAC est : `00:50:79:66:68:00`. (Capture d'écran : echange ARP).
    
    * Récapituler toutes les étapes (dans le compte-rendu, à l'écrit) quand PC1 exécute ping PC2 pour la première fois : 
    
      Lorsque PC2 cherche à ping PC1 pour la première fois, il va y avoir dans un premier temps une requête ARP sur sa broadcast car la table ARP de PC2 ne connaît pas encore l'addresse MAC de PC1, celle qui correspond à l'ip `10.2.1.1`. Une fois la requête envoyé, il va y avoir une "réponse" à cette dernière pour indiquer à quelle addresse MAC appartient l'addresse IP demandé par le ping. Une fois que le PC2 connait l'IP et la MAC de PC1 il va pouvoir lui envoyer des requêtes par protocole ICMP.
    
    * Expliquer...  
        * Pourquoi le switch n'a pas besoin d'IP : 
        
          Un switch ne connaît pas le protocol IP, il utilise principalement le protocole ethernet. Il a plus pour rôle de "multi-prise" pour lier différentes machines en stockant leur addresse MAC.

        * Pourquoi les machines ont besoin d'une IP pour pouvoir se ping :   
            
            Lorsqu'une machine veut ping une autre machine elle obtient son addresse MAC grâce à son IP en demandant un truc du genre : "A quelle addresse MAC correspond telle IP ??".

## II. More switches

* Mettre en place la topologie ci-dessus : 

    * Ajout de deux switches ainsi que d'une machine.  
    Modification des addresses IP aussi :  
        * PC1 : 10.2.2.1/24  
        * PC2 : 10.2.2.2/24  
        * PC3 : 10.2.2.3/24  

* Faire communiquer les trois PCs 
    * Ping de PC1 vers PC2 :    

        ```
        PC1> ping 10.2.2.2
        84 bytes from 10.2.2.2 icmp_seq=1 ttl=64 time=1.228 ms
        84 bytes from 10.2.2.2 icmp_seq=2 ttl=64 time=0.979 ms
        ```
            
    * Ping de PC1 vers PC3 :
        ```
        PC1> ping 10.2.2.3
        84 bytes from 10.2.2.3 icmp_seq=1 ttl=64 time=0.284 ms
        84 bytes from 10.2.2.3 icmp_seq=2 ttl=64 time=1.524 ms
        ```
    * Ping de PC2 vers PC3 :
        ```
        PC2> ping 10.2.2.3
        84 bytes from 10.2.2.3 icmp_seq=1 ttl=64 time=25.311 ms
        84 bytes from 10.2.2.3 icmp_seq=2 ttl=64 time=2.158 ms
        ```

* Analyser la table MAC d'un switch :

    Lorsqu'on utilise la commande `show mac address-table`, on peut voir les différentes addresses MAC stockées dans la `mac address table`.

    Trois lignes s'affichent après la commande :   
       
       ``` 
        IOU1#show mac address-table
                Mac Address Table
        -------------------------------------------
        Vlan    Mac Address       Type        Ports
        ----    -----------       --------    -----
        1    aabb.cc00.0210    DYNAMIC     Et0/2
        1    aabb.cc00.0300    DYNAMIC     Et0/1
        1    aabb.cc00.0320    DYNAMIC     Et0/2
        Total Mac Addresses for this criterion: 3

        ```

    Les trois lignes qui nous intéressent sont les addresses mac et les ports utilisés par nos trois machines.

*  Déterminer les informations STP :  

    * Utilisation de la commande `show spanning-tree` :
    ```
    IOU1#show spanning-tree
    VLAN0001
    Spanning tree enabled protocol rstp
    Root ID    Priority    32769
                Address     aabb.cc00.0100
                This bridge is the root
                Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

    Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
                Address     aabb.cc00.0100
                Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
                Aging Time  300 sec

    Interface           Role Sts Cost      Prio.Nbr Type
    ------------------- ---- --- --------- -------- --------------------------------
    Et0/0               Desg FWD 100       128.1    Shr 
    Et0/1               Desg FWD 100       128.2    Shr 
    Et0/2               Desg FWD 100       128.3    Shr 
    Et0/3               Desg FWD 100       128.4    Shr 
    Et1/0               Desg FWD 100       128.5    Shr 
    Et1/1               Desg FWD 100       128.6    Shr 
    Et1/2               Desg FWD 100       128.7    Shr 
    Et1/3               Desg FWD 100       128.8    Shr 
    Et2/0               Desg FWD 100       128.9    Shr 
    ```

    On peut voir que le premier switch est le RB car tous le role de ses ports sont en "Desg" et que leur status est en "FWD". En gros il peut communiquer avec tous le monde en utilisant tous ses ports. 

    On peut également remarquer que c'est sur le troisième switch qu'un port a été coupé pour éviter une broadcast storm, de plus, il a un port dedié à communiquer avec le RB. Ce port à pour role `root`.   


* Confirmer les informations STP:  
    
    Pour ce qui est du ping bon ça marche bien cependant j'ai pas vraiment trouvé quelque chose d'intéressant sur Wireshark pour te démontrer comment ça fonctionnait !

* Ainsi, déterminer quel lien a été désactivé par STP :  
  
  Pour déterminer quel lien a été désactivé on peut utiliser la commande `show spanning-tree` pour voir sur quel switch il y a un port avec le status `BLK` (blocked (je crois)). Pour ma part il s'agit du port `et0/2` sur le switch 3 :   
  ```
    IOU3#show spanning-tree

    VLAN0001
    Spanning tree enabled protocol rstp
    Root ID    Priority    32769
                Address     aabb.cc00.0100
                Cost        100
                Port        1 (Ethernet0/0)
                Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

    Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
                Address     aabb.cc00.0300
                Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
                Aging Time  300 sec

    Interface           Role Sts Cost      Prio.Nbr Type
    ------------------- ---- --- --------- -------- --------------------------------
    Et0/0               Root FWD 100       128.1    Shr 
    Et0/1               Desg FWD 100       128.2    Shr 
    Et0/2               Altn BLK 100       128.3    Shr 
    Et0/3               Desg FWD 100       128.4    Shr 
  ```

### Reconfigurer STP

* Changer la priorité d'un switch qui n'est pas le root bridge :  

    Par défault la priorité des ports est égale à 128. Pour changer la priorité d'un port on doit le configurer de la manière suivante :
    ``` 
    IOU2#configure terminal
    Enter configuration commands, one per line.  End with CNTL/Z.
    IOU2(config)#interface Et0/2               
    IOU2(config-if)#spanning-tree port-priority 64
    IOU2(config-if)#end
    ```

    Pour vérifier que la priorité à bien changé on retape la commande `show spanning-tree` pour vérifier la priorité du port (Et0/2 dans mon cas). La priorité de mon port est bien passé de `128.3` à `64.3`.

## III. Isolation

### 1. Simple

* Mettre en place la topologie ci-dessus avec des VLANs :   
  * PC1 : ip `10.2.2.1` / VLAN `10`   
  * PC1 : ip `10.2.2.2` / VLAN `20`   
  * PC1 : ip `10.2.2.3` / VLAN `20` 

    Pour les VLANs on va dans un premier temps les créer :  
    * Pour le vlan 10 :
        ```
        IOU1#conf t
        Enter configuration commands, one per line.  End with CNTL/Z.
        IOU1(config)#vlan 10
        IOU1(config-vlan)#name pc1
        IOU1(config-vlan)#exit
        ```
    * Pour le vlan 20 :
        ```
        IOU1#conf t
        Enter configuration commands, one per line.  End with CNTL/Z.
        IOU1(config)#vlan 20
        IOU1(config-vlan)#name pc2/pc3
        IOU1(config-vlan)#exit
        ```
    Ensuite on va aller configurer le port `e0/0` (celui qui relie le PC1 au switch) pour lui "attribuer" le VLAN 10 :  
    ```
    IOU1(config)#interface Ethernet 0/0
    IOU1(config-if)#switchport mode access
    IOU1(config-if)#switchport access vlan 10
    ```
    Puis on fait la même chose pour les ports `e0/1` et `e0/2` qui eux sont reliés au PC2 et PC3.

* Faire communiquer les PCs deux à deux :
    * vérifier que PC2 ne peut joindre que PC3 :
     
      Ping vers PC3 :
      ``` 
        PC2> ping 10.2.2.3
        84 bytes from 10.2.2.3 icmp_seq=1 ttl=64 time=0.421 ms
        84 bytes from 10.2.2.3 icmp_seq=2 ttl=64 time=0.626 ms
        84 bytes from 10.2.2.3 icmp_seq=3 ttl=64 time=0.593 ms
        84 bytes from 10.2.2.3 icmp_seq=4 ttl=64 time=0.581 ms
        84 bytes from 10.2.2.3 icmp_seq=5 ttl=64 time=0.883 ms
      ```
      Ping vers PC1 (impossible car pas le même VLAN) :
      ```
        PC2> ping 10.2.2.1
        host (10.2.2.1) not reachable
      ```
    * Vérifier que PC1 ne peut joindre personne alors qu'il est dans le même réseau (sad) :
    
      Ping vers PC2 :
      ```
        PC1> ping 10.2.2.2
        host (10.2.2.2) not reachable
      ```
    
      Ping vers PC3 :
      ```
        PC1> ping 10.2.2.3
        host (10.2.2.3) not reachable
      ```

### 2. Avec trunk

* Mettre en place la topologie ci-dessus (en utilisant des VLAN, vous aurez besoin de la notion de trunk) :  
  * PC1 : ip `10.2.10.1` / VLAN `10`   
  * PC2 : ip `10.2.20.1` / VLAN `20`   
  * PC3 : ip `10.2.10.2` / VLAN `10`    
  * PC4 : ip `10.2.20.2` / VLAN `20` 

* Faire communiquer les PCs deux à deux :   
   * Vérifier que PC1 ne peut joindre que PC3 :
     Ping vers PC2 : 
     ```
        PC1> ping 10.2.20.1
        host (255.255.255.0) not reachable
     ```
     Ping vers PC3 : 
     ```
        PC1> ping 10.2.10.2
        84 bytes from 10.2.10.2 icmp_seq=1 ttl=64 time=0.868 ms
        84 bytes from 10.2.10.2 icmp_seq=2 ttl=64 time=0.835 ms
     ```
   
   * Vérifier que PC2 ne peut joindre que PC4 :
     Ping vers PC1 :  
        
       On a vu qu'il n'y avait pas de retour lorsque PC1 essayait de ping PC2.   
     
     Ping vers PC4 : 
     ```
        PC2> ping 10.2.20.2
        84 bytes from 10.2.20.2 icmp_seq=1 ttl=64 time=0.705 ms
        84 bytes from 10.2.20.2 icmp_seq=2 ttl=64 time=0.959 ms
     ```

* Mettre en évidence l'utilisation des VLANs avec Wireshark :  
  J'ai pas vraiment trouvé quelque chose pour mettre en évidence ce point :/


## IV.Need perfs

* Mettre en place la topologie ci-dessus :  
  * Configurer LACP entre SW1 et SW2 :  
    Avant la configuration LACP des deux switchs :   
    ``` 
    IOU1#show etherchannel
        Channel-group listing: 
        ----------------------
    ```
    ``` 
    IOU2#show etherchannel
        Channel-group listing: 
        ----------------------
    ```
    La liste de nos groupes est vide !

    Après le configuration LACP on peut voir que le groupe 1 c'est bien ajouté ! 
    ``` 
    IOU1#show etherchannel
        Channel-group listing: 
        ----------------------

    Group: 1 
    ----------
    Group state = L2 
    Ports: 1   Maxports = 4
    Port-channels: 1 Max Port-channels = 4
    Protocol:   LACP
    Minimum Links: 0
    ```

  * Utiliser Wireshark pour mettre en évidence l'utilisation de trames LACP :  
    Toujours du mal avec wireshark ...
  
  * Vérifier avec un show ip interface po1 que la bande passante a bien été doublée :
  ``` 
  IOU1#show ip interface po1
  Port-channel1 is up, line protocol is up
  ```







         

    




