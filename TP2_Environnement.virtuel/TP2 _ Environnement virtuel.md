# TP2 : Environnement virtuel


## Compte-rendu

☀️ Sur **`node1.lan1.tp2`**


```
[alexandre@node1 ~]$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:b8:ef:44 brd ff:ff:ff:ff:ff:ff
    inet 10.1.1.11/24 brd 10.1.1.255 scope global noprefixroute enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:feb8:ef44/64 scope link
       valid_lft forever preferred_lft forever
```
       

```
[alexandre@node1 ~]$ ip route
10.1.1.0/24 dev enp0s8 proto kernel scope link src 10.1.1.11 metric 100
10.1.2.0/24 via 10.1.1.254 dev enp0s8
```

```
[alexandre@node1 ~]$ ping 10.1.2.12
PING 10.1.2.12 (10.1.2.12) 56(84) bytes of data.
64 bytes from 10.1.2.12: icmp_seq=1 ttl=63 time=2.07 ms
64 bytes from 10.1.2.12: icmp_seq=2 ttl=63 time=1.15 ms
64 bytes from 10.1.2.12: icmp_seq=3 ttl=63 time=1.21 ms
^C
--- 10.1.2.12 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2004ms
rtt min/avg/max/mdev = 1.146/1.472/2.066/0.420 ms
```

```
[alexandre@node1 ~]$ traceroute 10.1.2.12
traceroute to 10.1.2.12 (10.1.2.12), 30 hops max, 60 byte packets
 1  10.1.1.254 (10.1.1.254)  4.565 ms  4.321 ms  3.566 ms
 2  10.1.2.12 (10.1.2.12)  3.111 ms !X  2.817 ms !X  2.623 ms !X
 ```

# II. Interlude accès internet




☀️ **Sur `router.tp2`**


```
[alexandre@routeur ~]$ ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=112 time=24.4 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=112 time=22.8 ms
^C
--- 8.8.8.8 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1002ms
rtt min/avg/max/mdev = 22.831/23.614/24.397/0.783 ms
```

```
[alexandre@routeur ~]$ ping google.com
PING google.com (142.250.179.110) 56(84) bytes of data.
64 bytes from par21s20-in-f14.1e100.net (142.250.179.110): icmp_seq=1 ttl=115 time=24.1 ms
64 bytes from par21s20-in-f14.1e100.net (142.250.179.110): icmp_seq=2 ttl=115 time=25.0 ms
^C
--- google.com ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1002ms
rtt min/avg/max/mdev = 24.058/24.509/24.960/0.451 ms
```
☀️ **Accès internet LAN1 et LAN2**


```
[alexandre@node1 ~]$ sudo ip route add default via 10.1.1.254
[alexandre@node2 ~]$ sudo ip route add default via 10.1.1.254
```

```
[alexandre@node1 ~]$ sudo ip route add default via 10.1.2.254
[alexandre@node2 ~]$ sudo ip route add default via 10.1.2.254
```

```
[alexandre@node2 /]$ sudo nano etc/sysconfig/network-scripts/ifcfg-enp0s8
DNS=8.8.8.8
```

```
[alexandre@node2 /]$ ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=110 time=23.7 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=110 time=24.5 ms
^C
--- 8.8.8.8 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1003ms
rtt min/avg/max/mdev = 23.652/24.057/24.462/0.405 ms
```
 
```
[alexandre@node2 /]$ ping google.com
PING google.com (142.250.179.110) 56(84) bytes of data.
64 bytes from par21s20-in-f14.1e100.net (142.250.179.110): icmp_seq=1 ttl=114 time=23.9 ms
64 bytes from par21s20-in-f14.1e100.net (142.250.179.110): icmp_seq=2 ttl=114 time=23.0 ms
^C
--- google.com ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1002ms
rtt min/avg/max/mdev = 23.043/23.466/23.889/0.423 ms
```
# III. Services réseau


## 1. DHCP


---

☀️ **Sur `dhcp.lan1.tp2`**

```
[alexandre@dhcp network-scripts]$ sudo nano etc/sysconfig/network-scripts/ifcfg-enp0s8
IPADDR=10.1.1.253
```

  ```
  [alexandre@dhcp network-scripts]$ sudo dnf install dhcp
  [alexandre@dhcp network-scripts]$ sudo nano /etc/dhcp/dhcpd.conf
  [alexandre@dhcp network-scripts]$ sudo systemctl enable dhcpd
  [alexandre@dhcp network-scripts]$ sudo systemctl status dhcpd 
  dhcpd.service - DHCPv4 Server Daemon
     Loaded: loaded (/usr/lib/systemd/system/dhcpd.service; enabled; preset>
  ```

☀️ **Sur `node1.lan1.tp2`**



```
[alexandre@node1 /]$ sudo nano etc/sysconfig/network-scripts/ifcfg-enp0s8
```
```
[alexandre@node1 /]$ sudo cat etc/sysconfig/network-scripts/ifcfg-enp0s8
DEVICE=enp0s8
NAME=lan1
BOOTPROTO=dhcp
ONBOOT=yes
```
```
[alexandre@node1 /]$ ip a
[...]
 inet 10.1.1.100/24 brd 10.1.1.255 scope global secondary dynamic noprefixroute enp0s8
 ```
```
 [alexandre@node1 /]$ ping 10.1.2.11
PING 10.1.2.11 (10.1.2.11) 56(84) bytes of data.
64 bytes from 10.1.2.11: icmp_seq=1 ttl=63 time=1.24 ms
64 bytes from 10.1.2.11: icmp_seq=2 ttl=63 time=4.39 ms
^C
--- 10.1.2.11 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1004ms
rtt min/avg/max/mdev = 1.241/2.817/4.394/1.576 ms
```

## 2. Web web web


---

☀️ **Sur `web.lan2.tp2`**

```
[alexandre@node2 /]$ sudo hostnamectl set-hostname web.lan2.tp2
```
```
[alexandre@web ~]$ sudo dnf install nginx
[alexandre@web ~]$ sudo mkdir -p /var/www/site_nul
```
```
[alexandre@web ~]$ sudo cat /etc/nginx/conf.d/site_nul.conf
server {
    listen 80;
    server_name site_nul.tp2;

    root /var/www/site_nul;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```
```
[alexandre@web ~]$ sudo firewall-cmd --zone=public --permanent --add-service=http
success
[alexandre@web ~]$ sudo systemctl status nginx
● nginx.service - The nginx HTTP and reverse proxy server
     Loaded: loaded (/usr/lib/systemd/system/nginx.service; enabled; preset>
     Active: active (running) since Thu 2023-10-19 16:44:32 CEST; 3min 58s >
     [alexandre@web ~]$ ss -tulpn | grep 80
tcp   LISTEN 0      511          0.0.0.0:80        0.0.0.0:*
tcp   LISTEN 0      511             [::]:80           [::]:*
```

☀️ **Sur `node1.lan1.tp2`**


```
[alexandre@web /]$ sudo nano etc/host
```
```
[alexandre@web /]$ sudo curl 10.1.2.12
<html>
<head><title>403 Forbidden</title></head>
<body>
<center><h1>403 Forbidden</h1></center>
<hr><center>nginx/1.20.1</center>
</body>
</html>
```

