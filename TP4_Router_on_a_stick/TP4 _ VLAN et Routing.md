
🌞 **Adressage**
```
PC1> ip 10.1.10.1/24
PC2> ip 10.1.10.2/24
adm1> ip 10.1.20.1/24
[alexandre@web ~]$ cat /etc/sysconfig/network-scripts/ifcfg-enp0s8
DEVICE=enp0s8
BOOTPROTO=static
IPADDR=192.168.1.4
NETMASK=255.255.255.0

```


🌞 **Configuration des VLANs**

```
sw1(config)#vlan 10 
sw1(config-vlan)#name clients
sw1(config)#interface ethernet0/0
sw1(config-if)#switchport mode access
sw1(config-if)#switchport access vlan 10
sw1(config)#interface Ethernet0/1
sw1(config-if)#switchport mode access
sw1(config-if)#switchport access vlan 10

sw1(config)#vlan 20
sw1(config-vlan)#name admins
sw1(config)#interface Ethernet0/2
sw1(config-if)#switchport mode access
sw1(config-if)#switchport access vlan 20

sw1(config)#vlan 30
sw1(config-vlan)#name servers
sw1(config)#interface Ethernet0/3
sw1(config-if)#switchport mode access
sw1(config-if)#switchport access vlan 30



sw1#show interface trunk
Port        Mode             Encapsulation  Status        Native vlan
Et1/0       on               802.1q         trunking      1

Port        Vlans allowed on trunk
Et1/0       1-4094

Port        Vlans allowed and active in management domain
Et1/0       1,10,20,30

Port        Vlans in spanning tree forwarding state and not pruned
Et1/0       1,10,20,30
```



```
R1(config)#interface fastethernet0/0.10
R1(config-subif)#encapsulation dot1Q 10
R1(config-subif)#ip addr 10.1.10.254 255.255.255.0


R1(config)#interface fastethernet0/0.20
R1(config-subif)#encapsulation dot1Q 20
R1(config-subif)#ip addr 10.1.20.254 255.255.255.0

R1(config)#interface fastethernet0/0.30
R1(config-subif)#encapsulation dot1Q 30
R1(config-subif)#ip addr 10.1.30.254 255.255.255.0
```
```
PC1> ping 10.1.10.254/24
10.1.10.254 icmp_seq=1 timeout
84 bytes from 10.1.10.254 icmp_seq=2 ttl=255 time=10.552 ms
84 bytes from 10.1.10.254 icmp_seq=3 ttl=255 time=5.804 ms
84 bytes from 10.1.10.254 icmp_seq=4 ttl=255 time=5.649 ms

PC2> ping 10.1.10.254

84 bytes from 10.1.10.254 icmp_seq=1 ttl=255 time=8.099 ms
84 bytes from 10.1.10.254 icmp_seq=2 ttl=255 time=3.484 ms


adm1> ping 10.1.20.254

84 bytes from 10.1.20.254 icmp_seq=1 ttl=255 time=4.267 ms
84 bytes from 10.1.20.254 icmp_seq=2 ttl=255 time=4.601 ms

[alexandre@web ~]$ ping 10.1.30.254
PING 10.1.30.254 (10.1.30.254) 56(84) bytes of data.
64 bytes from 10.1.30.254: icmp_seq=1 ttl=255 time=2.45 ms
64 bytes from 10.1.30.254: icmp_seq=2 ttl=255 time=7.22 ms
```

```
PC1> ip 10.1.10.1 10.1.10.254
PC2> ip 10.1.10.2 10.1.10.254
adm1> ip 10.1.20.1 10.1.20.254
[alexandre@web ~]$ cat /etc/sysconfig/network-scripts/ifcfg-enp0s3
DEVICE=enp0s3
BOOTPROTO=static
ONBOOT=yes
IPADDR=10.1.30.1
NETMASK=255.255.255.0
DNS1=8.8.8.8
GATEWAY=10.1.30.254
```
```
PC1> ping 10.1.20.1

84 bytes from 10.1.20.1 icmp_seq=1 ttl=63 time=23.472 ms
84 bytes from 10.1.20.1 icmp_seq=2 ttl=63 time=13.723 ms

PC2> ping 10.1.30.1

84 bytes from 10.1.30.1 icmp_seq=1 ttl=63 time=20.171 ms
84 bytes from 10.1.30.1 icmp_seq=2 ttl=63 time=20.718 ms

adm1> ping 10.1.10.1

84 bytes from 10.1.10.1 icmp_seq=1 ttl=63 time=30.758 ms

[alexandre@web ~]$ ping 10.1.10.2
PING 10.1.10.2 (10.1.10.2) 56(84) bytes of data.
64 bytes from 10.1.10.2: icmp_seq=1 ttl=63 time=13.7 ms
```

# II. NAT



🌞 **Ajoutez le noeud Cloud à la topo**


```
R1#conf t
R1(config)#interface fastethernet0/1
R1(config-if)#ip address dhcp
R1(config-if)#no shut
R1#show ip interface br
FastEthernet0/1            10.0.3.16       YES DHCP   up                    up

R1#ping 1.1.1.1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 1.1.1.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 40/67/92 ms
```
🌞 **Configurez le NAT**
```
R1(config)#interface fastethernet0/0
R1(config-if)#ip nat inside

R1(config)#interface fastethernet0/1
R1(config-if)#ip nat outside

R1(config)#access-list 1 permit any
R1(config)#ip nat inside source list 1 interface fastethernet0/0 overload
```

🌞 **Test**

```
PC1> ping google.com
google.com resolved to 142.250.178.142
84 bytes from 142.250.178.142 icmp_seq=1 ttl=116 time=50.654 ms

PC2> ip dns 8.8.8.8
PC2> ping google.com
google.com resolved to 142.250.201.174
84 bytes from 142.250.201.174 icmp_seq=1 ttl=118 time=113.050 ms
84 bytes from 142.250.201.174 icmp_seq=2 ttl=118 time=44.504 ms

[alexandre@web ~]$ ping google.com
PING google.com (142.250.201.174) 56(84) bytes of data.
64 bytes from par21s23-in-f14.1e100.net (142.250.201.174): icmp_seq=1 ttl=112 time=113 ms
64 bytes from par21s23-in-f14.1e100.net (142.250.201.174): icmp_seq=2 ttl=112 time=59.4 msadm1> ip dns 8.8.8.8

adm1> ping google.com
google.com resolved to 216.58.214.174
84 bytes from 216.58.214.174 icmp_seq=1 ttl=118 time=48.702 ms
```

# III. Add a building


```
sw1#show running-config
Building configuration...

Current configuration : 1635 bytes
!
! Last configuration change at 12:19:58 UTC Sun Nov 26 2023
!
version 15.2
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
service compress-config
!

hostname sw1
!
boot-start-marker
boot-end-marker

logging discriminator EXCESS severity drops 6 msg-body drops EXCESSCOLL
logging buffered 50000
logging console discriminator EXCESS
!
no aaa new-model
!
no ip icmp rate-limit unreachable
!
no ip domain-lookup
no ip cef
no ipv6 cef
!
spanning-tree mode pvst
spanning-tree extend system-id
!
interface Ethernet0/0
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet0/1
 switchport access vlan 10
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet0/2
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet0/3
!
interface Ethernet1/0
!
interface Ethernet1/1
!
interface Ethernet1/2
!
interface Ethernet1/3
!
interface Ethernet2/0
!
interface Ethernet2/1
!
interface Ethernet2/2
!
interface Ethernet2/3
!
interface Ethernet3/0
!
interface Ethernet3/1
!
interface Ethernet3/2
!
interface Ethernet3/3
!
interface Vlan1
 no ip address
 shutdown
!
ip forward-protocol nd
!
ip tcp synwait-time 5
ip http server
!
ip ssh server algorithm encryption aes128-ctr aes192-ctr aes256-ctr
ip ssh client algorithm encryption aes128-ctr aes192-ctr aes256-ctr
!
control-plane
!
line con 0
 exec-timeout 0 0
 privilege level 15
 logging synchronous
line aux 0
 exec-timeout 0 0
 privilege level 15
 logging synchronous
line vty 0 4
 login
```
```

sw2#show running-config
Building configuration...

Current configuration : 1693 bytes
!
! Last configuration change at 12:19:58 UTC Sun Nov 26 2023
!
version 15.2
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
service compress-config
!
hostname sw2
!
boot-start-marker
boot-end-marker
!
logging discriminator EXCESS severity drops 6 msg-body drops EXCESSCOLL
logging buffered 50000
logging console discriminator EXCESS
!
no aaa new-model
!
no ip icmp rate-limit unreachable
!
no ip domain-lookup
no ip cef
no ipv6 cef
!
spanning-tree mode pvst
spanning-tree extend system-id
!
interface Ethernet0/0
 switchport access vlan 10
 switchport mode access
!
interface Ethernet0/1
 switchport access vlan 10
 switchport mode access
!
interface Ethernet0/2
 switchport access vlan 20
 switchport mode access
!
interface Ethernet0/3
 switchport access vlan 30
!
interface Ethernet1/0
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet1/0.10
!
interface Ethernet1/1
!
interface Ethernet1/2
!
interface Ethernet1/3
!
interface Ethernet2/0
!
interface Ethernet2/1
!
interface Ethernet2/2
!
interface Ethernet2/3
!
interface Ethernet3/0
!
interface Ethernet3/1
!
interface Ethernet3/2
!
interface Ethernet3/3
!
interface Vlan1
 no ip address
 shutdown
!
ip forward-protocol nd
!
ip tcp synwait-time 5
ip http server
!
ip ssh server algorithm encryption aes128-ctr aes192-ctr aes256-ctr
ip ssh client algorithm encryption aes128-ctr aes192-ctr aes256-ctr
!
control-plane
!
line con 0
 exec-timeout 0 0
 privilege level 15
 logging synchronous
line aux 0
 exec-timeout 0 0
 privilege level 15
 logging synchronous
line vty 0 4
 login
!
end

```

```
sw3#show running-config
Building configuration...

Current configuration : 1629 bytes
!
! Last configuration change at 12:19:58 UTC Sun Nov 26 2023
!
version 15.2
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
service compress-config
!
hostname sw3
!
boot-start-marker
boot-end-marker
!
logging discriminator EXCESS severity drops 6 msg-body drops EXCESSCOLL
logging buffered 50000
logging console discriminator EXCESS
!
no aaa new-model
!
no ip icmp rate-limit unreachable
!
no ip domain-lookup
no ip cef
no ipv6 cef
!
spanning-tree mode pvst
spanning-tree extend system-id
!
interface Ethernet0/0
 switchport access vlan 10
 switchport mode access
!
interface Ethernet0/1
 switchport access vlan 10
 switchport mode access
!
interface Ethernet0/2
 switchport access vlan 10
 switchport mode access
!
interface Ethernet0/3
 switchport access vlan 10
 switchport mode access
!
interface Ethernet1/0
!
interface Ethernet1/1
!
interface Ethernet1/2
!
interface Ethernet1/3
!
interface Ethernet2/0
!
interface Ethernet2/1
!
interface Ethernet2/2
!
interface Ethernet2/3
!
interface Ethernet3/0
!
interface Ethernet3/1
!
interface Ethernet3/2
!
interface Ethernet3/3
!
interface Vlan1
 no ip address
 shutdown
!
ip forward-protocol nd
!
ip tcp synwait-time 5
ip http server
!
ip ssh server algorithm encryption aes128-ctr aes192-ctr aes256-ctr
ip ssh client algorithm encryption aes128-ctr aes192-ctr aes256-ctr
!
control-plane
!
line con 0
 exec-timeout 0 0
 privilege level 15
 logging synchronous
line aux 0
 exec-timeout 0 0
 privilege level 15
 logging synchronous
line vty 0 4
 login
!
end

```
```
R1#show running-config
Building configuration...

Current configuration : 1484 bytes
!
version 12.4
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
!
hostname R1
!
boot-start-marker
boot-end-marker
!
no aaa new-model
memory-size iomem 5
no ip icmp rate-limit unreachable
ip cef
!
no ip domain lookup
!
multilink bundle-name authenticated
!
archive
 log config
  hidekeys
!
ip tcp synwait-time 5
!
interface FastEthernet0/0
 no ip address
 ip nat inside
 ip virtual-reassembly
 duplex auto
 speed auto
!
interface FastEthernet0/0.10
 encapsulation dot1Q 10
 ip address 10.1.10.254 255.255.255.0
!
interface FastEthernet0/0.20
 encapsulation dot1Q 20
 ip address 10.1.20.254 255.255.255.0
!
interface FastEthernet0/0.30
 encapsulation dot1Q 30
 ip address 10.1.30.254 255.255.255.0
!
interface FastEthernet0/1
 ip address dhcp
 ip nat outside
 ip virtual-reassembly
 duplex auto
 speed auto
!
interface FastEthernet1/0
 no ip address
 shutdown
 duplex auto
 speed auto
!
interface FastEthernet2/0
 no ip address
 shutdown
 duplex auto
 speed auto
!
ip forward-protocol nd
!
no ip http server
no ip http secure-server
ip nat inside source list 1 interface FastEthernet0/0 overload
!
access-list 1 permit any
no cdp log mismatch duplex
!
control-plane
!
line con 0
 exec-timeout 0 0
 privilege level 15
 logging synchronous
line aux 0
 exec-timeout 0 0
 privilege level 15
 logging synchronous
line vty 0 4
 login
!
!
end
```

```
[alexandre@clone ~]$ sudo cat /etc/dhcp/dhcpd.conf
subnet 10.1.10.0 netmask 255.255.255.0 {
    range 10.1.10.100 10.1.10.200;
    option routers 10.1.10.254;
    option domain-name-servers 8.8.8.8, 8.8.4.4;
}
```
```
PC4> show ip

NAME        : PC4[1]
IP/MASK     : 10.1.10.101/24
GATEWAY     : 10.1.10.254
DNS         : 8.8.8.8  8.8.4.4
DHCP SERVER : 10.1.10.253
DHCP LEASE  : 41709, 43200/21600/37800
MAC         : 00:50:79:66:68:04
LPORT       : 20034
RHOST:PORT  : 127.0.0.1:20035
MTU         : 1500


PC3> dhcp
DORA IP 10.1.10.100/24 GW 10.1.10.254

PC3> ping 10.1.20.1

84 bytes from 10.1.20.1 icmp_seq=2 ttl=63 time=15.627 ms
84 bytes from 10.1.20.1 icmp_seq=3 ttl=63 time=13.343 ms
84 bytes from 10.1.20.1 icmp_seq=4 ttl=63 time=15.856 ms

[alexandre@clone ~]$ ping 10.1.20.1
PING 10.1.20.1 (10.1.20.1) 56(84) bytes of data.
64 bytes from 10.1.20.1: icmp_seq=1 ttl=63 time=29.0 ms

PC4> ping 10.1.30.1

84 bytes from 10.1.30.1 icmp_seq=1 ttl=63 time=19.717 ms
84 bytes from 10.1.30.1 icmp_seq=2 ttl=63 time=17.422 ms

PC3> ping goole.com
goole.com resolved to 217.160.0.201

84 bytes from 217.160.0.201 icmp_seq=1 ttl=52 time=61.786 ms
84 bytes from 217.160.0.201 icmp_seq=2 ttl=52 time=57.889 ms

PC3> ping 1.1.1.1

84 bytes from 1.1.1.1 icmp_seq=1 ttl=55 time=69.686 ms


PC3> ping ynov.com
ynov.com resolved to 104.26.10.233

84 bytes from 104.26.10.233 icmp_seq=1 ttl=55 time=50.465 ms

```
