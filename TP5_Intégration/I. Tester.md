

🌞 **Récupérer l'application dans la VM `hosting.tp5.b1`**

[alexandre@hosting ~]$ ls
calculatrice_client.py  calculatrice_server.py

🌞 **Essayer de lancer l'app**
```
[alexandre@hosting ~]$ ls
calculatrice_client.py  calculatrice_server.py
[alexandre@hosting ~]$ cat calculatrice_server.py | grep py
                    help="Usage: python bs_server.py 

[alexandre@hosting ~]$ python calculatrice_server.py

python calculatrice_server.py
```

[alexandre@hosting ~]$ ss -tul | grep :13337
tcp   LISTEN 0      1            0.0.0.0:13337      0.0.0.0:*

> Si des erreurs apparaissent au lancement du serveur, il faudra les traiter !

🌞 **Tester l'app depuis `hosting.tp5.b1`**

```
[alexandre@hosting ~]$ sudo python calculatrice_client.py
Veuillez saisir une opération arithmétique : 8+8

Un client ('127.0.0.1', 45270) s'est connecté.
Le client ('127.0.0.1', 45270) a envoyé 8+8
Réponse envoyée au client ('127.0.0.1', 45270) : 16.

```


# II. Intégrer


## 1. Environnement



🌞 **Créer un dossier /opt/calculatrice**
```
[alexandre@hosting calculatrice]$ sudo mkdir /opt/calculatrice/
```
## 2. systemd service


```bash
$ sudo systemctl start calculatrice
$ sudo systemctl enable calculatrice

$ sudo journalctl -xe -u calculatrice
```

### B. Service basique

🌞 **Créer le fichier 
```
$ sudo cat /etc/systemd/system/calculatrice.service
[Unit]
Description=Super calculatrice réseau

[Service]
ExecStart=/usr/bin/python /opt/calculatrice/bs_server.py

[Install]
WantedBy=multi-user.target

[alexandre@hosting calculatrice]$ ls -al /etc/systemd/system/calculatrice.service
-rw-r--r--. 1 root root 152 Nov 28 14:19 /etc/systemd/system/calculatrice.service
```


🌞 **Démarrer le service**
```
[alexandre@hosting calculatrice]$ sudo systemctl start calculatrice

[alexandre@hosting calculatrice]$ sudo systemctl status calculatrice
● calculatrice.service - Super calculatrice réseau
     Loaded: loaded (/etc/systemd/system/calculatrice.service; enabled; preset: disabled)
     Active: active (running) since Tue 2023-11-28 14:43:26 CET; 4min 0s ago
   Main PID: 1919 (python)
      Tasks: 1 (limit: 11052)
     Memory: 5.6M
        CPU: 23ms
     CGroup: /system.slice/calculatrice.service
             └─1919 /usr/bin/python /opt/calculatrice/bs_server.py
             
[alexandre@hosting calculatrice]$ ss -tulpn | grep 13337
tcp   LISTEN 0      1            0.0.0.0:13337      0.0.0.0:* 
             
```
### C. Amélioration du service

➜ **Redémarrage automatique**

- si un jour le service est down, pour n'importe quelle raison, il serait bon qu'il redémarre automatiquement
- c'est une des features de systemd, on va configurer ça !

🌞 **Configurer une politique de redémarrage** dans le fichier `calculatrice.service`
```
[alexandre@hosting calculatrice]$ cat /etc/systemd/system/calculatrice.service
[Unit]
Description=Super calculatrice réseau

[Service]
ExecStart=/usr/bin/python /opt/calculatrice/bs_server.py
Restart=on-failure
RestartSec=30

[Install]
WantedBy=multi-user.target
```



🌞 **Tester que la politique de redémarrage fonctionne**

```
[alexandre@hosting calculatrice]$ sudo systemctl status calculatrice | grep PID
   Main PID: 2090 (python)
[alexandre@hosting calculatrice]$ sudo kill -9 2090

[alexandre@hosting calculatrice]$ sudo systemctl status calculatrice
● calculatrice.service - Super calculatrice réseau
     Loaded: loaded (/etc/systemd/system/calculatrice.service; enabled; preset: disabled)
     Active: active (running) since Tue 2023-11-28 14:59:57 CET; 3s ago
   
   ```
---

➜ **Firewall !**

- pour le moment vous avez fait tous les tests localement
  - donc on a pas eu besoin de toucher au firewall
  - le firewall de Rocky Linux ne bloque aucun traffic depuis/vers `127.0.0.1`
- quand on va héberger l'app sur le réseau de l'école et accueillir des clients potentiels, il faudra ouvrir un port firewall

🌞 **Ouverture automatique du firewall** dans le fichier 

```
[alexandre@hosting calculatrice]$ sudo cat /etc/systemd/system/calculatrice.service
[Unit]
Description=Super calculatrice réseau

[Service]
ExecStartPre=/usr/bin/firewall-cmd --add-port=13337/tcp
ExecStart=/usr/bin/python /opt/calculatrice/bs_server.py
Restart=on-failure
RestartSec=30
ExecStopPost=/user/bin/firewall-cmd --remove-port=13337/tcp
[Install]
WantedBy=multi-user.target
```

🌞 **Vérifier l'ouverture automatique du firewall**
```
[alexandre@hosting calculatrice]$ sudo firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s3 enp0s8
  sources:
  services: cockpit dhcpv6-client ssh
  ports: 13337/tcp
  protocols:
  forward: yes
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
  ```
  ```
PS C:\Users\Alexandre\test> py .\calculatrice_client.py
Veuillez saisir une opération arithmétique : 347+34
2023-11-28 17:47:09 INFO R�ponse re�ue du serveur 192.168.1.19 : b'381'
```

## 3. Monitoring

🌞 **Installer Netdata** sur `hosting.tp5.b1`

```

[alexandre@hosting ~]$sudo wget -O /tmp/netdata-kickstart.sh https://my-netdata.io/kickstart.sh && sh /tmp/netdata-kickstart.sh

[alexandre@hosting ~]$ sudo firewall-cmd --permanent --add-port=19999/tcp
```
🌞 **Configurer une sonde TCP**

```
[alexandre@hosting netdata]$ cd /etc/netdata 2>/dev/null || cd /opt/netdata/etc/netdata
sudo ./edit-config go.d/portcheck.conf
[alexandre@hosting netdata]$ sudo cat go.d/portcheck.conf
[sudo] password for alexandre:
## All available configuration options, their descriptions and default values:
## https://github.com/netdata/go.d.plugin/tree/master/modules/portcheck

#update_every: 1
#autodetection_retry: 0
#priority: 70000

jobs:
  - name: server1
    host: 127.0.0.1
    ports:
      - 13337

# - name: job2
#   host: 6.0.0.2
#   ports: [22, 1999]
````

🌞 **Alerting Discord**
```
[alexandre@hosting netdata]$ cd /etc/netdata 2>/dev/null || cd /opt/netdata/etc/netdata
[alexandre@hosting netdata]$ sudo ./edit-config health_alarm_notify.conf


```
![capture-sonde-TCP](./capute-TCP-discord.jpg)

# III. Héberger

🌞 **Conf serveur SSH**


```
[alexandre@hosting ~]$ sudo ss -tulpn | grep sshd
[sudo] password for alexandre:
tcp   LISTEN 0      128     192.168.1.19:22         0.0.0.0:*    users:(("sshd",pid=1776,fd=3))
```

## 4. Serveur Calculatrice

🌞 **Conf serveur Calculatrice**

```
[alexandre@hosting ~]$ sudo ss -tulpn | grep 10.0.2.15
tcp   LISTEN 0      1          10.0.2.15:13337      0.0.0.0:*    users:(("python",pid=1708,fd=4))
```
