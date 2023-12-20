[Schéma infrastructure réseaux](./schema_visuel.vpd)

[tableau d'adressage](tableau_d'adressage_TP8)

conf gns switch et routeur
```

R3#show run
Building configuration...

Current configuration : 2097 bytes
!
version 12.4
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
!
hostname R3
!
boot-start-marker
boot-end-marker
!
!
no aaa new-model
memory-size iomem 5
no ip icmp rate-limit unreachable
ip cef
!
!
!
!
no ip domain lookup
!
multilink bundle-name authenticated
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
archive
 log config
  hidekeys
!
!
!
!
ip tcp synwait-time 5
!
!
!
!
interface FastEthernet0/0
 ip address dhcp
 duplex auto
 speed auto
!
interface FastEthernet0/1
 no ip address
 shutdown
 duplex auto
 speed auto
!
interface FastEthernet1/0
 no ip address
 ip nat inside
 ip virtual-reassembly
 duplex auto
 speed auto
!
interface FastEthernet1/0.1
 encapsulation dot1Q 10
 ip address 10.1.1.254 255.255.255.240
!
interface FastEthernet1/0.2
 encapsulation dot1Q 2
 ip address 10.1.2.254 255.255.255.192
!
interface FastEthernet1/0.3
 encapsulation dot1Q 3
 ip address 10.1.3.254 255.255.255.248
!
interface FastEthernet1/0.4
 encapsulation dot1Q 4
 ip address 10.1.4.254 255.255.255.240
!
interface FastEthernet1/0.5
 encapsulation dot1Q 5
 ip address 10.1.5.254 255.255.255.224
!
interface FastEthernet1/0.6
 encapsulation dot1Q 6
 ip address 10.1.6.254 255.255.255.240
!
interface FastEthernet1/0.7
 encapsulation dot1Q 7
 ip address 10.1.7.254 255.255.255.224
!
interface FastEthernet1/0.8
 encapsulation dot1Q 8
 ip address 10.1.8.254 255.255.255.248
!
interface FastEthernet1/0.10
 encapsulation dot1Q 100
 ip address 10.1.10.254 255.255.254.0
!
interface FastEthernet1/0.12
 encapsulation dot1Q 12
 ip address 10.1.12.254 255.255.254.0
!
interface FastEthernet1/0.14
 encapsulation dot1Q 14
 ip address 10.1.14.254 255.255.255.240
!
interface FastEthernet2/0
 no ip address
 shutdown
 duplex auto
 speed auto
!
ip forward-protocol nd
!
!
no ip http server
no ip http secure-server
!
no cdp log mismatch duplex
!
!
!
!
!
!
control-plane
!
!







IOU2#show run
Building configuration...

Current configuration : 1984 bytes
!
! Last configuration change at 21:11:16 UTC Wed Dec 20 2023
!
version 15.2
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
service compress-config
!
hostname IOU2
!
boot-start-marker
boot-end-marker
!
!
logging discriminator EXCESS severity drops 6 msg-body drops EXCESSCOLL
logging buffered 50000
logging console discriminator EXCESS
!
no aaa new-model
!
!
!
!
!
no ip icmp rate-limit unreachable
!
!
!
no ip domain-lookup
no ip cef
no ipv6 cef
!
!
!
spanning-tree mode pvst
spanning-tree extend system-id
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
interface Ethernet0/0
 switchport trunk allowed vlan 10
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet0/1
 switchport trunk allowed vlan 10,20,30,40,50,60,70,80,100,120,140
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet0/2
!
interface Ethernet0/3
 switchport access vlan 10
 switchport mode access
!
interface Ethernet1/0
!
interface Ethernet1/1
 switchport access vlan 10
 switchport trunk allowed vlan 10,20,30,40,50,60,70,80,100,120,140
 switchport trunk encapsulation dot1q
 switchport mode access
!
interface Ethernet1/2
!
interface Ethernet1/3
 switchport trunk allowed vlan 10,20,30,40,50,60,70,80,100,120,140
 switchport trunk encapsulation dot1q
 switchport mode trunk
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






IOU1#show run
Building configuration...

Current configuration : 1938 bytes
!
! Last configuration change at 21:11:16 UTC Wed Dec 20 2023
!
version 15.2
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
service compress-config
!
hostname IOU1
!
boot-start-marker
boot-end-marker
!
!
logging discriminator EXCESS severity drops 6 msg-body drops EXCESSCOLL
logging buffered 50000
logging console discriminator EXCESS
!
no aaa new-model
!
!
!
!
!
no ip icmp rate-limit unreachable
!
!
!
no ip domain-lookup
no ip cef
no ipv6 cef
!
!
!
spanning-tree mode pvst
spanning-tree extend system-id
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
interface Ethernet0/0
 switchport trunk allowed vlan 10,20,30,40,50,60,70,80,100,120,140
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet0/1
 switchport trunk allowed vlan 10,20,30,40,50,60,70,80,100,120,140
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet0/2
 switchport trunk allowed vlan 10,20,30,40,50,60,70,80,100,120,140
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet0/3
 switchport trunk allowed vlan 10,20,30,40,50,60,70,80,100,120,140
 switchport trunk encapsulation dot1q
 switchport mode trunk
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
ip ssh client alg







IOU5#show run
Building configuration...

Current configuration : 1682 bytes
!
! Last configuration change at 21:11:16 UTC Wed Dec 20 2023
!
version 15.2
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
service compress-config
!
hostname IOU5
!
boot-start-marker
boot-end-marker
!
!
logging discriminator EXCESS severity drops 6 msg-body drops EXCESSCOLL
logging buffered 50000
logging console discriminator EXCESS
!
no aaa new-model
!
!
!
!
!
no ip icmp rate-limit unreachable
!
!
!
no ip domain-lookup
no ip cef
no ipv6 cef
!
!
!
spanning-tree mode pvst
spanning-tree extend system-id
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
interface Ethernet0/0
 switchport trunk allowed vlan 10,20,30,40,50,60,70,80,100,120,140
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet0/1
 switchport trunk allowed vlan 10,20,30,40,50,60,70,80,100,120,140
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet0/2
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








IOU3#show run
Building configuration...

Current configuration : 1810 bytes
!
! Last configuration change at 21:11:16 UTC Wed Dec 20 2023
!
version 15.2
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
service compress-config
!
hostname IOU3
!
boot-start-marker
boot-end-marker
!
!
logging discriminator EXCESS severity drops 6 msg-body drops EXCESSCOLL
logging buffered 50000
logging console discriminator EXCESS
!
no aaa new-model
!
!
!
!
!
no ip icmp rate-limit unreachable
!
!
!
no ip domain-lookup
no ip cef
no ipv6 cef
!
!
!
spanning-tree mode pvst
spanning-tree extend system-id
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
interface Ethernet0/0
 switchport trunk allowed vlan 10,20,30,40,50,60,70,80,100,120,140
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet0/1
 switchport trunk allowed vlan 10,20,30,40,50,60,70,80,100,120,140
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet0/2
 switchport trunk allowed vlan 10,20,30,40,50,60,70,80,100,120,140
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







IOU4#show run
Building configuration...

Current configuration : 1912 bytes
!
! Last configuration change at 21:11:16 UTC Wed Dec 20 2023
!
version 15.2
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
service compress-config
!
hostname IOU4
!
boot-start-marker
boot-end-marker
!
!
logging discriminator EXCESS severity drops 6 msg-body drops EXCESSCOLL
logging buffered 50000
logging console discriminator EXCESS
!
no aaa new-model
!
!
!
!
!
no ip icmp rate-limit unreachable
!
!
!
no ip domain-lookup
no ip cef
no ipv6 cef
!
!
!
spanning-tree mode pvst
spanning-tree extend system-id
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
interface Ethernet0/0
 switchport trunk allowed vlan 10,20,30,40,50,60,70,80,100,120,140
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet0/1
 switchport trunk allowed vlan 10,20,30,40,50,60,70,80,100,120,140
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet0/2
 switchport trunk allowed vlan 10,20,30,40,50,60,70,80,100,120,140
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet0/3
 switchport access vlan 70
 switchport mode access
!
interface Ethernet1/0
 switchport access vlan 70
 switchport mode access
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
!
!
!
!
control-plane








R2#show run
Building configuration...

Current configuration : 2041 bytes
!
version 12.4
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
!
hostname R2
!
boot-start-marker
boot-end-marker
!
!
no aaa new-model
memory-size iomem 5
no ip icmp rate-limit unreachable
ip cef
!
!
!
!
no ip domain lookup
!
multilink bundle-name authenticated
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
archive
 log config
  hidekeys
!
!
!
!
ip tcp synwait-time 5
!
!
!
!
interface FastEthernet0/0
 no ip address
 duplex auto
 speed auto
!
interface FastEthernet0/1
 no ip address
 shutdown
 duplex auto
 speed auto
!
interface FastEthernet1/0
 no ip address
 ip nat outside
 ip virtual-reassembly
 duplex auto
 speed auto
!
interface FastEthernet1/0.1
 encapsulation dot1Q 10
 ip address 10.2.1.254 255.255.255.240
!
interface FastEthernet1/0.2
 encapsulation dot1Q 20
 ip address 10.2.2.254 255.255.255.192
!
interface FastEthernet1/0.3
 encapsulation dot1Q 30
 ip address 10.2.3.254 255.255.255.240
!
interface FastEthernet1/0.4
 encapsulation dot1Q 40
 ip address 10.2.4.254 255.255.255.248
!
interface FastEthernet1/0.5
 encapsulation dot1Q 50
 ip address 10.2.5.254 255.255.255.240
!
interface FastEthernet1/0.6
 encapsulation dot1Q 60
 ip address 10.2.6.254 255.255.255.192
!
interface FastEthernet1/0.7
 encapsulation dot1Q 70
 ip address 10.2.7.254 255.255.255.128
!
interface FastEthernet1/0.8
 encapsulation dot1Q 80
 ip address 10.2.8.254 255.255.255.240
!
interface FastEthernet1/0.9
 encapsulation dot1Q 90
 ip address 10.2.9.254 255.255.255.240
!
interface FastEthernet1/0.10
 encapsulation dot1Q 100
 ip address 10.2.10.254 255.255.255.240
!
interface FastEthernet2/0
 ip address dhcp
 ip nat outside
 ip virtual-reassembly
 duplex auto
 speed auto
!
ip forward-protocol nd
!
!
no ip http server
no ip http secure-server
!
no cdp log mismatch duplex
!
!
!
!
!
!
control-plane
!
!
!
!
!
!
!
!
!
!
line con 0
 exec-timeout 0 0





IOU7#show run
Building configuration...

Current configuration : 1600 bytes
!
! Last configuration change at 21:11:16 UTC Wed Dec 20 2023
!
version 15.2
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
service compress-config
!
hostname IOU7
!
boot-start-marker
boot-end-marker
!
!
logging discriminator EXCESS severity drops 6 msg-body drops EXCESSCOLL
logging buffered 50000
logging console discriminator EXCESS
!
no aaa new-model
!
!
!
!
!
no ip icmp rate-limit unreachable
!
!
!
no ip domain-lookup
no ip cef
no ipv6 cef
!
!
!
spanning-tree mode pvst
spanning-tree extend system-id
!
!
!
!
!
!
!
!
!
!
!
!
!
!
!
interface Ethernet0/0
 switchport trunk allowed vlan 10,20,30,40,50,60,70,80,90,100
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet0/1
 switchport access vlan 10
 switchport mode access
!
interface Ethernet0/2
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
!
!
!
!
control-plane
!

```