

🌞 **Commençons simple**
```
PC1> ip 10.3.1.1
PC2> ip 10.3.1.2
```
```
PC1> ping 10.3.1.2

84 bytes from 10.3.1.2 icmp_seq=1 ttl=64 time=0.500 ms
84 bytes from 10.3.1.2 icmp_seq=2 ttl=64 time=0.287 ms
```
```
IOU6#show mac address-table
          Mac Address Table
-------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       --------    -----
```


> Jusque là, ça devrait aller. Noter qu'on a fait aucune conf sur le switch. Tant qu'on ne fait rien, c'est une bête multiprise.

# II. VLAN

**Le but dans cette partie va être de tester un peu les *VLANs*.**

On va rajouter **un troisième client** qui, bien que dans le même réseau, sera **isolé des autres grâce aux *VLANs***.

**Les *VLANs* sont une configuration à effectuer sur les *switches*.** C'est les *switches* qui effectuent le blocage.

Le principe est simple :

- déclaration du VLAN sur tous les switches
  - un VLAN a forcément un ID (un entier)
  - bonne pratique, on lui met un nom
- sur chaque switch, on définit le VLAN associé à chaque port
  - genre "sur le port 35, c'est un client du VLAN 20 qui est branché"

![VLAN FOR EVERYONE](./img/get_a_vlan.jpg)

## 1. Topologie 2

![Topologie 2](./img/topo2.png)

## 2. Adressage topologie 2

| Node  | IP            | VLAN |
| ----- | ------------- | ---- |
| `pc1` | `10.3.1.1/24` | 10   |
| `pc2` | `10.3.1.2/24` | 10   |
| `pc3` | `10.3.1.3/24` | 20   |

### 3. Setup topologie 2

🌞 **Adressage**
```
PC3> ip 10.3.1.3
Checking for duplicate address...
PC3 : 10.3.1.3 255.255.255.0
```
```
PC1> ping 10.3.1.2

84 bytes from 10.3.1.2 icmp_seq=1 ttl=64 time=1.326 ms

PC3> ping 10.3.1.1

84 bytes from 10.3.1.1 icmp_seq=1 ttl=64 time=0.273 ms

PC2> ping 10.3.1.3

84 bytes from 10.3.1.3 icmp_seq=1 ttl=64 time=0.506 ms

```

🌞 **Configuration des VLANs**

```
IOU6(config)#vlan 10
IOU6(config-vlan)#name admins
IOU6(config-vlan)#exit

IOU6(config)#vlan 20
IOU6(config-vlan)#name guests
IOU6(config-vlan)#exit
```
```

IOU6(config)#interface Ethernet0/0
IOU6(config-if)#switchport mode acces
IOU6(config-if)#switchport access vlan 10
IOU6(config-if)#exit
IOU6(config)#interface Ethernet 0/1
IOU6(config-if)#switchport mode acces
IOU6(config-if)#switch acces vlan 10
IOU6(config-if)#switchport acces vlan 10
IOU6(config-if)#exit
IOU6(config)#interface Ethernet0/2
IOU6(config-if)#switchport mode acces
IOU6(config-if)#switchport acces vlan 20
IOU6(config-if)#exit
```

🌞 **Vérif**
```
PC1> ping 10.3.1.2
84 bytes from 10.3.1.2 icmp_seq=1 ttl=64 time=0.204 ms
84 bytes from 10.3.1.2 icmp_seq=2 ttl=64 time=0.332 ms
^C
PC1> ping 10.3.1.3
host (10.3.1.3) not reachable
```

# III. Ptite VM DHCP

On va ajouter une VM dans la topologie, histoire que vous voyiez cet aspect de GNS.

| Node          | IP              | VLAN |
| ------------- | --------------- | ---- |
| `pc1`         | `10.3.1.1/24`   | 10   |
| `pc2`         | `10.3.1.2/24`   | 10   |
| `pc3`         | `10.3.1.3/24`   | 20   |
| `pc4`         | X               | 20   |
| `pc5`         | X               | 10   |
| `dhcp.tp3.b2` | `10.3.1.253/24` | 20   |

🌞 **VM `dhcp.tp3.b2`**
```
[alexandre@dhcp ~]$ sudo systemctl status dhcpd
● dhcpd.service - DHCPv4 Server Daemon
     Loaded: loaded (/usr/lib/systemd/system/dhcpd.service; enabled; preset: disabled)
     Active: active (running) since Fri 2023-11-10 10:54:57 CET; 2min 45s ago
       Docs: man:dhcpd(8)
             man:dhcpd.conf(5)
   Main PID: 1338 (dhcpd)
     Status: "Dispatching packets..."
      Tasks: 1 (limit: 11052)
     Memory: 4.6M
        CPU: 13ms
     CGroup: /system.slice/dhcpd.service
             └─1338 /usr/sbin/dhcpd -f -cf /etc/dhcp/dhcpd.conf -user dhcpd -group dhcpd --no-pid

Nov 10 10:54:57 dhcp.tp3.b2 dhcpd[1338]:
Nov 10 10:54:57 dhcp.tp3.b2 dhcpd[1338]: Listening on LPF/enp0s3/08:00:27:93:93:fa/10.3.1.0/24
Nov 10 10:54:57 dhcp.tp3.b2 dhcpd[1338]: Sending on   LPF/enp0s3/08:00:27:93:93:fa/10.3.1.0/24
Nov 10 10:54:57 dhcp.tp3.b2 dhcpd[1338]: Sending on   Socket/fallback/fallback-net
Nov 10 10:54:57 dhcp.tp3.b2 dhcpd[1338]: Server starting service.
Nov 10 10:54:57 dhcp.tp3.b2 systemd[1]: Started DHCPv4 Server Daemon.
Nov 10 10:56:05 dhcp.tp3.b2 dhcpd[1338]: DHCPDISCOVER from 00:50:79:66:68:03 via enp0s3
Nov 10 10:56:06 dhcp.tp3.b2 dhcpd[1338]: DHCPOFFER on 10.3.1.100 to 00:50:79:66:68:03 (PC4) via enp0s3
Nov 10 10:56:09 dhcp.tp3.b2 dhcpd[1338]: DHCPREQUEST for 10.3.1.100 (10.3.1.253) from 00:50:79:66:68:03 (PC4) via enp0s3
Nov 10 10:56:09 dhcp.tp3.b2 dhcpd[1338]: DHCPACK on 10.3.1.100 to 00:50:79:66:68:03 (PC4) via enp0s3
```
```
[alexandre@dhcp ~]$ sudo cat /etc/dhcp/dhcpd.conf
subnet 10.3.1.0 netmask 255.255.255.0 {
    range 10.3.1.100 10.3.1.200;
    option routers 10.3.1.1;
    option domain-name-servers 8.8.8.8, 8.8.4.4;
}
```
```
PC4> dhcp
DDORA IP 10.3.1.100/24 GW 10.3.1.1
```
```
PC5> dhcp
DDD
Can't find dhcp server
```


> Pour rappel, la trame DHCP Discover part en broadcast. Le switch bloque ça aussi, il bloque tout, il s'en fout de la nature de la trame : si ça passe d'un port taggé VLAN X à un port taggé VLAN Y, ça dégage.