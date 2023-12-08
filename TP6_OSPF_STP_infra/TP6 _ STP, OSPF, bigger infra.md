# TP6 : STP, OSPF, bigger infra


🌞 **Configurer STP sur les 3 switches**
```
IOU3#show spanning-tree

VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     aabb.cc00.0400
             Cost        100
             Port        3 (Ethernet0/2)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     aabb.cc00.0600
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  300 sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/0               Desg FWD 100       128.1    P2p
Et0/1               Altn BLK 100       128.2    P2p
Et0/2               Root FWD 100       128.3    P2p
Et0/3               Desg FWD 100       128.4    P2p
Et1/0               Desg FWD 100       128.5    P2p
Et1/1               Desg FWD 100       128.6    P2p
Et1/2               Desg FWD 100       128.7    P2p
Et1/3               Desg FWD 100       128.8    P2p
Et2/0               Desg FWD 100       128.9    P2p
Et2/1               Desg FWD 100       128.10   P2p
Et2/2               Desg FWD 100       128.11   P2p
Et2/3               Desg FWD 100       128.12   P2p
Et3/0               Desg FWD 100       128.13   P2p
Et3/1               Desg FWD 100       128.14   P2p
Et3/2               Desg FWD 100       128.15   P2p
Et3/3               Desg FWD 100       128.16   P2p
```


🌞 **Altérer le spanning-tree** en désactivant un port

```
IOU3#(config)#interface ethernet0/2
IOU3#(config-if)#shutdown
```

```
IOU3#show spanning-tree

VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     aabb.cc00.0400
             Cost        200
             Port        2 (Ethernet0/1)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     aabb.cc00.0600
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  15  sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/0               Desg FWD 100       128.1    P2p
Et0/1               Root LIS 100       128.2    P2p
Et0/3               Desg FWD 100       128.4    P2p
Et1/0               Desg FWD 100       128.5    P2p
Et1/1               Desg FWD 100       128.6    P2p
Et1/2               Desg FWD 100       128.7    P2p
Et1/3               Desg FWD 100       128.8    P2p
Et2/0               Desg FWD 100       128.9    P2p
Et2/1               Desg FWD 100       128.10   P2p
Et2/2               Desg FWD 100       128.11   P2p
Et2/3               Desg FWD 100       128.12   P2p
Et3/0               Desg FWD 100       128.13   P2p
Et3/1               Desg FWD 100       128.14   P2p
Et3/2               Desg FWD 100       128.15   P2p
Et3/3               Desg FWD 100       128.16   P2p


```
> **Ré-activer ce port après la manip** inutile de le laisser désactivé.

🌞 **Altérer le spanning-tree** en modifiant le coût d'un lien

```
SW1(config-if)#spanning-tree cost 200
```
[capture STP](./capture-STP.pcapng)

## II. OSPF

OSPF donc, routage dynamique.

On va se cantonner à le setup de façon simple, et ensuite on mettra en place un service qui consomme ce routage en partie III.

![Topo OSPF](./img/topo_ospf.png)

> Ce sont les *areas* OSPF qui sont représentées en couleur, pas des réseaux. 🌸

➜ Tableau d'adressage

- la logique de l'adressage que je vous propose :
  - choix de masque
    - du `/24` pour les réseaux où y'a des clients
      - classique, simple
    - du `/30` pour les réseaux entre les routeurs
      - comme ça, on permet vraiment explicitement que deux IPs sur ces réseaux
  - choix des octets
    - `10.6.` pour les deux premiers octets
      - 10 pas chiant comme d'hab
      - 6 pour TP6 comme d'hab
    - pour le troisième octet
      - entre les routeurs : `10.6.13.` l'octet qui suit :
        - 13 indique le réseau entre le routeur 1 et le routeur 3
        - 13 et pas 31 parce que je lis de gauche à droite perso
      - réseaux clients : `10.6.1.`
        - arbitraire, y'a un réseau 1, un réseau 2, etc.

| Node          | `10.6.1.0/24` | `10.6.2.0/24` | `10.6.3.0/24` | `10.6.41.0/30` | `10.6.13.0/30` | `10.6.21.0/30` | `10.6.23.0/30` | `10.6.52.0/30` |
| ------------- | ------------- | ------------- | ------------- | -------------- | -------------- | -------------- | -------------- | -------------- |
| `waf.tp6.b1`  | `10.6.1.11`   | ❌            | ❌            | ❌             | ❌             | ❌             | ❌             | ❌             |
| `dhcp.tp6.b1` | `10.6.1.253`  | ❌            | ❌            | ❌             | ❌             | ❌             | ❌             | ❌             |
| `meo.tp6.b1`  | ❌            | `10.6.2.11`   | ❌            | ❌             | ❌             | ❌             | ❌             | ❌             |
| `john.tp6.b1` | ❌            | ❌            | `10.6.3.11`   | ❌             | ❌             | ❌             | ❌             | ❌             |
| `R1`          | ❌            | ❌            | `10.6.3.254`  | `10.6.41.1`    | `10.6.13.1`    | `10.6.21.1`    | ❌             | ❌             |
| `R2`          | ❌            | ❌            | ❌            | ❌             | ❌             | `10.6.21.2`    | `10.6.23.2`    | `10.6.52.2`    |
| `R3`          | ❌            | ❌            | ❌            | ❌             | `10.6.13.2`    | ❌             | `10.6.23.1`    | ❌             |
| `R4`          | `10.6.1.254`  | `10.6.2.254`  | ❌            | `10.6.41.2`    | ❌             | ❌             | ❌             | ❌             |
| `R5`          | ❌            | ❌            | ❌            | ❌             | ❌             | ❌             | ❌             | `10.6.52.1`    |

🌞 **Montez la topologie**
```
PC3> show ip

NAME        : PC3[1]
IP/MASK     : 10.6.1.11/24
GATEWAY     : 10.6.1.254
DNS         :
MAC         : 00:50:79:66:68:02
LPORT       : 20057
RHOST:PORT  : 127.0.0.1:20058
MTU         : 1500

PC1> show ip

NAME        : PC1[1]
IP/MASK     : 10.6.2.11/24
GATEWAY     : 10.6.2.254
DNS         :
MAC         : 00:50:79:66:68:04
LPORT       : 20053
RHOST:PORT  : 127.0.0.1:20054
MTU         : 1500


PC2> show ip

NAME        : PC2[1]
IP/MASK     : 10.6.3.11/24
GATEWAY     : 10.6.3.254
DNS         :
MAC         : 00:50:79:66:68:01
LPORT       : 20055
RHOST:PORT  : 127.0.0.1:20056
MTU         : 1500

PC3> ping 10.6.2.11

84 bytes from 10.6.2.11 icmp_seq=1 ttl=63 time=30.171 ms
84 bytes from 10.6.2.11 icmp_seq=2 ttl=63 time=20.238 ms


```
🌞 **Configurer OSPF sur tous les routeurs**

```
R1#show running-config
Building configuration...

Current configuration : 1375 bytes
!
interface FastEthernet0/0
 ip address 10.6.3.254 255.255.255.0
 duplex auto
 speed auto
!
interface FastEthernet0/1
 ip address 10.6.41.1 255.255.255.252
 duplex auto
 speed auto
!
interface FastEthernet1/0
 ip address 10.6.21.1 255.255.255.252
 duplex auto
 speed auto
!
interface FastEthernet2/0
 ip address 10.6.13.1 255.255.255.252
 duplex auto
 speed auto
!
router ospf 1
 router-id 1.1.1.1
 log-adjacency-changes
 network 10.6.1.0 0.0.0.255 area 0
 network 10.6.3.0 0.0.0.255 area 3
 network 10.6.13.0 0.0.0.3 area 0
 network 10.6.13.0 0.0.0.255 area 0
 network 10.6.21.0 0.0.0.3 area 0
 network 10.6.41.0 0.0.0.3 area 0

```
```
R1#show ip ospf neighbor

Neighbor ID     Pri   State           Dead Time   Address         Interface
4.4.4.4           1   FULL/DR         00:00:35    10.6.41.2       FastEthernet0/1
2.2.2.2           1   FULL/DR         00:00:36    10.6.21.2       FastEthernet1/0
3.3.3.3           1   FULL/DR         00:00:37    10.6.13.2       FastEthernet2/

R1#show ip route
Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route

Gateway of last resort is not set

     10.0.0.0/8 is variably subnetted, 8 subnets, 2 masks
C       10.6.13.0/30 is directly connected, FastEthernet2/0
O       10.6.1.0/24 [110/20] via 10.6.41.2, 00:21:58, FastEthernet0/1
O       10.6.2.0/24 [110/20] via 10.6.41.2, 00:21:58, FastEthernet0/1
C       10.6.3.0/24 is directly connected, FastEthernet0/0
C       10.6.21.0/30 is directly connected, FastEthernet1/0
O       10.6.23.0/30 [110/11] via 10.6.21.2, 00:21:48, FastEthernet1/0
                     [110/11] via 10.6.13.2, 00:21:48, FastEthernet2/0
C       10.6.41.0/30 is directly connected, FastEthernet0/1
O       10.6.52.0/30 [110/2] via 10.6.21.2, 00:21:49, FastEthernet1/0

R2#show ip ospf neighbor

Neighbor ID     Pri   State           Dead Time   Address         Interface
5.5.5.5           1   FULL/BDR        00:00:33    10.6.52.1       FastEthernet1/0
3.3.3.3           1   FULL/DR         00:00:35    10.6.23.1       FastEthernet0/1
1.1.1.1           1   FULL/BDR        00:00:36    10.6.21.1       FastEthernet0/0

R2#show ip route
Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route

Gateway of last resort is not set

     10.0.0.0/8 is variably subnetted, 8 subnets, 2 masks
O       10.6.13.0/30 [110/11] via 10.6.21.1, 00:24:23, FastEthernet0/0
O       10.6.1.0/24 [110/30] via 10.6.21.1, 00:24:23, FastEthernet0/0
O       10.6.2.0/24 [110/30] via 10.6.21.1, 00:24:23, FastEthernet0/0
O IA    10.6.3.0/24 [110/20] via 10.6.21.1, 00:24:23, FastEthernet0/0
C       10.6.21.0/30 is directly connected, FastEthernet0/0
C       10.6.23.0/30 is directly connected, FastEthernet0/1
O       10.6.41.0/30 [110/20] via 10.6.21.1, 00:24:25, FastEthernet0/0
C       10.6.52.0/30 is directly connected, FastEthernet1/0

R3#show ip ospf neighbor

Neighbor ID     Pri   State           Dead Time   Address         Interface
2.2.2.2           1   FULL/BDR        00:00:39    10.6.23.2       FastEthernet0/1
1.1.1.1           1   FULL/BDR        00:00:32    10.6.13.1       FastEthernet0/0

R3#show ip route
Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route

Gateway of last resort is not set

     10.0.0.0/8 is variably subnetted, 8 subnets, 2 masks
C       10.6.13.0/30 is directly connected, FastEthernet0/0
O       10.6.1.0/24 [110/30] via 10.6.13.1, 00:24:55, FastEthernet0/0
O       10.6.2.0/24 [110/30] via 10.6.13.1, 00:24:55, FastEthernet0/0
O IA    10.6.3.0/24 [110/20] via 10.6.13.1, 00:24:55, FastEthernet0/0
O       10.6.21.0/30 [110/11] via 10.6.13.1, 00:24:55, FastEthernet0/0
C       10.6.23.0/30 is directly connected, FastEthernet0/1
O       10.6.41.0/30 [110/20] via 10.6.13.1, 00:24:57, FastEthernet0/0
O       10.6.52.0/30 [110/11] via 10.6.23.2, 00:24:57, FastEthernet0/1

R4#show ip ospf neighbor

Neighbor ID     Pri   State           Dead Time   Address         Interface
1.1.1.1           1   FULL/BDR        00:00:37    10.6.41.1       FastEthernet1/0

R4#show ip route
Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route

Gateway of last resort is not set

     10.0.0.0/8 is variably subnetted, 8 subnets, 2 masks
O       10.6.13.0/30 [110/2] via 10.6.41.1, 00:25:19, FastEthernet1/0
C       10.6.1.0/24 is directly connected, FastEthernet0/0
C       10.6.2.0/24 is directly connected, FastEthernet0/1
O IA    10.6.3.0/24 [110/11] via 10.6.41.1, 00:25:29, FastEthernet1/0
O       10.6.21.0/30 [110/2] via 10.6.41.1, 00:25:19, FastEthernet1/0
O       10.6.23.0/30 [110/12] via 10.6.41.1, 00:25:19, FastEthernet1/0
C       10.6.41.0/30 is directly connected, FastEthernet1/0
O       10.6.52.0/30 [110/3] via 10.6.41.1, 00:25:20, FastEthernet1/0

R5#show ip ospf neighbor

Neighbor ID     Pri   State           Dead Time   Address         Interface
2.2.2.2           1   FULL/DR         00:00:39    10.6.52.2       FastEthernet0/

R5#show ip route
Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route

Gateway of last resort is not set

     10.0.0.0/8 is variably subnetted, 8 subnets, 2 masks
O       10.6.13.0/30 [110/21] via 10.6.52.2, 00:10:57, FastEthernet0/0
O       10.6.1.0/24 [110/40] via 10.6.52.2, 00:10:57, FastEthernet0/0
O       10.6.2.0/24 [110/40] via 10.6.52.2, 00:10:57, FastEthernet0/0
O IA    10.6.3.0/24 [110/30] via 10.6.52.2, 00:10:57, FastEthernet0/0
O       10.6.21.0/30 [110/20] via 10.6.52.2, 00:10:57, FastEthernet0/0
O       10.6.23.0/30 [110/20] via 10.6.52.2, 00:10:57, FastEthernet0/0
O       10.6.41.0/30 [110/30] via 10.6.52.2, 00:10:58, FastEthernet0/0
C       10.6.52.0/30 is directly connected, FastEthernet0/0

```

🌞 **Test**

```
PC3> ping 10.6.52.1

84 bytes from 10.6.52.1 icmp_seq=1 ttl=252 time=72.900 ms
84 bytes from 10.6.52.1 icmp_seq=2 ttl=252 time=59.952 ms
84 bytes from 10.6.52.1 icmp_seq=3 ttl=252 time=60.111 ms


PC3> ping 8.8.8.8

*10.6.1.254 icmp_seq=1 ttl=255 time=3.764 ms (ICMP type:3, code:1, Destination host unreachable)

R1#ping 10.6.1.11

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.6.1.11, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 20/32/44 ms

PC2> ping 10.6.52.1

84 bytes from 10.6.52.1 icmp_seq=1 ttl=253 time=51.243 ms
84 bytes from 10.6.52.1 icmp_seq=2 ttl=253 time=45.361 ms
```


🦈 **`tp6_ospf.pcapng`**
[reference](./ospf-paquet)

On peux voir que le routeur a pour destination 224.0.0.5 qui est l'ip pour dire qu'il communique avec plusieurs routeurs en même temps grace a ospf

> *Un BPDU c'est juste le nom qu'on donne à une trame OSPF échangée entre deux routeurs.*

## III. DHCP relay

```
[alexandre@clone ~]$ sudo cat /etc/dhcp/dhcpd.conf
subnet 10.6.1.0 netmask 255.255.255.0 {
    range 10.6.1.100 10.6.1.200;
    option routers 10.6.1.254;
    option domain-name-servers 1.1.1.1;
}

subnet 10.6.3.0 netmask 255.255.255.0 {
    range 10.6.3.100 10.6.3.200;
    option routers 10.6.3.254;
    option domain-name-servers 1.1.1.1;
}

[alexandre@clone ~]$ sudo systemctl status dhcpd
● dhcpd.service - DHCPv4 Server Daemon
     Loaded: loaded (/usr/lib/systemd/system/dhcpd.service; enabled; preset: disabled)
     Active: active (running) since Fri 2023-12-08 00:24:12 CET; 1min 12s ago
```


🌞 **Configurer un DHCP relay sur la passerelle de John**

- vérifier que Waf et John peuvent récupérer une IP en DHCP
- check les logs du serveur DHCP pour voir les DORA
  - je veux ces 4 lignes de logs dans le compte-rendu
  - pour John et pour Waf
- la conf sur le routeur qui est la passerelle de John c'est :

```
R1(config)#interface f
R1(config)#interface fastEthernet 0/0
R1(config-if)#ip helper-address 10.6.1.253

PC3> dhcp
DDORA IP 10.6.1.100/24 GW 10.6.1.254

PC2> dhcp
DDORA10.6.1.253 IP 10.6.3.100/24 GW 10.6.3.254

[alexandre@clone etc]$ journalctl -u dhcpd
Dec 08 13:19:01 clone.hostname systemd[1]: Started DHCPv4 Server Daemon.
Dec 08 13:21:02 clone.hostname dhcpd[766]: DHCPDISCOVER from 00:50:79:66:68:01 via 10.6.3.254
Dec 08 13:21:03 clone.hostname dhcpd[766]: DHCPOFFER on 10.6.3.100 to 00:50:79:66:68:01 (PC2) via 10.6.3.254
Dec 08 13:21:05 clone.hostname dhcpd[766]: DHCPREQUEST for 10.6.3.100 (10.6.1.253) from 00:50:79:66:68:01 (PC2) via 10.>
Dec 08 13:21:05 clone.hostname dhcpd[766]: DHCPACK on 10.6.3.100 to 00:50:79:66:68:01 (PC2) via 10.6.3.254
Dec 08 13:25:09 clone.hostname dhcpd[766]: DHCPDISCOVER from 00:50:79:66:68:02 via enp0s3
Dec 08 13:25:10 clone.hostname dhcpd[766]: DHCPOFFER on 10.6.1.100 to 00:50:79:66:68:02 (PC3) via enp0s3
Dec 08 13:25:13 clone.hostname dhcpd[766]: DHCPREQUEST for 10.6.1.100 (10.6.1.253) from 00:50:79:66:68:02 (PC3) via enp>
Dec 08 13:25:13 clone.hostname dhcpd[766]: DHCPACK on 10.6.1.100 to 00:50:79:66:68:02 (PC3) via enp0s
```

> *Ui c'est tout. Bah... quoi de plus ? Il a juste besoin de savoir à qui faire passer les requêtes !*

## IV. Bonus

### 1. ACL

C'est un peu moche que les clients puissent `ping` les IPs des routeurs de l'autre côté de l'infra.

Normalement, il peut joindre sa passerelle, internet, épuicétou.

🌞 **Configurer une access-list**

- ça se fait sur les routeurs
- le but :
  - les clients peuvent ping leur passerelle
  - et internet
  - épuicétou

![it ain't one](./img/it_aint_one.jpg)