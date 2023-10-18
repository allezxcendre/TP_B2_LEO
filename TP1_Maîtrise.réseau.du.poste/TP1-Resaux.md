# TP1 : Maîtrise réseau du poste

Pour ce TP, on va utiliser **uniquement votre poste** (pas de VM, rien, quedal, quetchi).

Le but du TP : se remettre dans le bain tranquillement en manipulant pas mal de concepts qu'on a vu l'an dernier.

C'est un premier TP *chill*, qui vous(ré)apprend à maîtriser votre poste en ce qui concerne le réseau. Faites le seul ou avec votre mate préféré bien sûr, mais jouez le jeu, faites vos propres recherches.

La "difficulté" va crescendo au fil du TP, mais la solution tombe très vite avec une ptite recherche Google si vos connaissances de l'an dernier deviennent floues.

- [TP1 : Maîtrise réseau du poste](#tp1--maîtrise-réseau-du-poste)
- [I. Basics](#i-basics)
- [II. Go further](#ii-go-further)
- [III. Le requin](#iii-le-requin)

# I. Basics

> Tout est à faire en ligne de commande, sauf si précision contraire.

☀️ **Carte réseau WiFi**

Déterminer...

- l'adresse MAC de votre carte WiFi
```
C:\Users\Alexandre>ipconfig all
Adresse physique . . : 2C-6D-C1-37-81-FB
```
- l'adresse IP de votre carte WiFi
 ```
C:\Users\Alexandre>ipconfig all
10.33.76.187
```
- le masque de sous-réseau du réseau LAN auquel vous êtes connectés en WiFi
  - en notation CIDR, par exemple `/16`
 ```
  C:\Users\Alexandre>ipconfig all
 255.255.240.0
 /20
```
---

☀️ **Déso pas déso**

Pas besoin d'un terminal là, juste une feuille, ou votre tête, ou un tool qui calcule tout hihi. Déterminer...

- l'adresse de réseau du LAN auquel vous êtes connectés en WiFi
10.33.76.187
- l'adresse de broadcast
 10.33.79.255
- le nombre d'adresses IP disponibles dans ce réseau
Nombre d'adresses IP disponibles : 4096
---

☀️ **Hostname**

- déterminer le hostname de votre PC
```
C:\Users\Alexandre>hostname
Alexandre
```

---

☀️ **Passerelle du réseau**

Déterminer...
- l'adresse IP de la passerelle du réseau
- l'adresse MAC de la passerelle du réseau
```
C:\Users\Alexandre>ipconfig /all
[...]
10.33.79.254

 C:\Users\Alexandre>arp -a
 [...]
 10.33.79.254          7c-5a-1c-d3-d8-76     dynamique
 ```

---

☀️ **Serveur DHCP et DNS**

Déterminer...

- l'adresse IP du serveur DHCP qui vous a filé une IP
- l'adresse IP du serveur DNS que vous utilisez quand vous allez sur internet
```
 Je vais dans le detail de mes reseaux pour trouver 
DHCP : 10.33.79.254 
DNS : 8.8.8.8
```
---

☀️ **Table de routage**

Déterminer...

- dans votre table de routage, laquelle est la route par défaut

```
C:\Users\Alexandre>netstat -r
[...]
IPv4 Table de routage
===========================================================================
Itinéraires actifs :
Destination réseau    Masque réseau  Adr. passerelle   Adr. interface Métrique
          0.0.0.0          0.0.0.0     10.33.79.254     10.33.76.187     30
```

---

![Not sure](./img/notsure.png)

# II. Go further

> Toujours tout en ligne de commande.

---

☀️ **Hosts ?**

- faites en sorte que pour votre PC, le nom `b2.hello.vous` corresponde à l'IP `1.1.1.1`
- prouvez avec un `ping b2.hello.vous` que ça ping bien `1.1.1.1`

> Vous pouvez éditer en GUI, et juste me montrer le contenu du fichier depuis le terminal pour le compte-rendu.

```

PS C:\Users\Alexandre> ping b2.hello.vous

Envoi d’une requête 'ping' sur b2.hello.vous [1.1.1.1] avec 32 octets de données :
Réponse de 1.1.1.1 : octets=32 temps=12 ms TTL=57
Réponse de 1.1.1.1 : octets=32 temps=19 ms TTL=57
Réponse de 1.1.1.1 : octets=32 temps=12 ms TTL=57
Réponse de 1.1.1.1 : octets=32 temps=15 ms TTL=57

Statistiques Ping pour 1.1.1.1:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 12ms, Maximum = 19ms, Moyenne = 14ms
    
   ```
    
---

☀️ **Go mater une vidéo youtube et déterminer, pendant qu'elle tourne...**

- l'adresse IP du serveur auquel vous êtes connectés pour regarder la vidéo
```
PS C:\Users\Alexandre> ping www.youtube.com
[...]
Réponse de 142.250.180.142
```
- le port du serveur auquel vous êtes connectés
- le port que votre PC a ouvert en local pour se connecter au port du serveur distant
```
 PS C:\Users\Alexandre> netstat -an
 [...]
 UDP    0.0.0.0:52845          142.250.180.142:443
 port de youtube:443        mon port: 52845
```

> Il est **fortement** recommandé de couper toutes vos autres connexions internet pour identifier facilement ce trafic (fermez Discord, tous les onglets de vos navigateurs ouverts, etc. Fermez tout ce qui sollicite le réseau.

---

☀️ **Requêtes DNS**

Déterminer...

- à quelle adresse IP correspond le nom de domaine `www.ynov.com`
```
PS C:\Users\Alexandre> ping ynov.com
 [...]
Réponse de 104.26.10.233
```

- à quel nom de domaine correspond l'IP `174.43.238.89`

> Ca s'appelle faire un "reverse lookup DNS".
```
PS C:\Users\Alexandre> nslookup 174.43.238.89
[...]
Nom :    89.sub-174-43-238.myvzw.com
```

---

☀️ **Hop hop hop**

Déterminer...

- par combien de machines vos paquets passent quand vous essayez de joindre `www.ynov.com`
```
PS C:\Users\Alexandre> tracert ynov.com

Détermination de l’itinéraire vers ynov.com [172.67.74.226]
avec un maximum de 30 sauts :

  1     3 ms     1 ms     1 ms  10.33.79.254
  2     3 ms     2 ms    11 ms  145.117.7.195.rev.sfr.net [195.7.117.145]
  3     5 ms     3 ms     3 ms  237.195.79.86.rev.sfr.net [86.79.195.237]
  4    11 ms    19 ms    10 ms  196.224.65.86.rev.sfr.net [86.65.224.196]
  5    12 ms    22 ms    13 ms  12.148.6.194.rev.sfr.net [194.6.148.12]
  6    10 ms    11 ms    10 ms  12.148.6.194.rev.sfr.net [194.6.148.12]
  7    12 ms    10 ms    11 ms  141.101.67.48
  8    11 ms    11 ms    20 ms  172.71.124.4
  9    12 ms    10 ms    10 ms  172.67.74.226
  
  je passe part 9 machines avant d'arriver a ynov.com
```
---

☀️ **IP publique**

Déterminer...

- l'adresse IP publique de la passerelle du réseau (le routeur d'YNOV donc si vous êtes dans les locaux d'YNOV quand vous faites le TP)
```
PS C:\Users\Alexandre> nslookup myip.opendns.com. resolver1.opendns.com
Serveur :   dns.opendns.com
Address:  208.67.222.222
```
---

☀️ **Scan réseau**

Déterminer...

- combien il y a de machines dans le LAN auquel vous êtes connectés

> Allez-y mollo, on va vite flood le réseau sinon. :)
```
PS C:\Users\Alexandre> arp -a
[...]
j'en compte 36
```
![Stop it](./img/stop.png)

# III. Le requin

Faites chauffer Wireshark. Pour chaque point, je veux que vous me livrez une capture Wireshark, format `.pcap` donc.

Faites *clean* 🧹, vous êtes des grands now :

- livrez moi des captures réseau avec uniquement ce que je demande et pas 40000 autres paquets autour
  - vous pouvez sélectionner seulement certains paquets quand vous enregistrez la capture dans Wireshark
- stockez les fichiers `.pcap` dans le dépôt git et côté rendu Markdown, vous me faites un lien vers le fichier, c'est cette syntaxe :

```markdown
[Lien vers capt]![voial mon wireshark](https://hackmd.io/_uploads/r1BfjiL-p.png)

```

---

☀️ **Capture ARP**

- 📁 fichier `arp.pcap`
- capturez un échange ARP entre votre PC et la passerelle du réseau

> Si vous utilisez un filtre Wireshark pour mieux voir ce trafic, précisez-le moi dans le compte-rendu.
filtre : arp
---

[Capture ARP](https://github.com/allezxcendre/TP_B2_LEO/blob/main/TP1_Ma%C3%AEtrise.r%C3%A9seau.du.poste/arp.pcap.pcapng)

☀️ **Capture DNS**

- 📁 fichier `dns.pcap`
- capturez une requête DNS vers le domaine de votre choix et la réponse
- vous effectuerez la requête DNS en ligne de commande

> Si vous utilisez un filtre Wireshark pour mieux voir ce trafic, précisez-le moi dans le compte-rendu.
filtre : dns
---

[Capture DNS](https://github.com/allezxcendre/TP_B2_LEO/blob/main/TP1_Ma%C3%AEtrise.r%C3%A9seau.du.poste/dns.pcap.pcapng)

☀️ **Capture TCP**

- 📁 fichier `tcp.pcap`
- effectuez une connexion qui sollicite le protocole TCP
- je veux voir dans la capture :
  - un 3-way handshake
  - un peu de trafic
  - la fin de la connexion TCP

> Si vous utilisez un filtre Wireshark pour mieux voir ce trafic, précisez-le moi dans le compte-rendu.
filtre : tcp
---

[Capture TCP](https://github.com/allezxcendre/TP_B2_LEO/blob/main/TP1_Ma%C3%AEtrise.r%C3%A9seau.du.poste/tcp.pcap.pcapng)

![Packet sniffer](img/wireshark.jpg)

> *Je sais que je vous l'ai déjà servi l'an dernier lui, mais j'aime trop ce meme hihi 🐈*